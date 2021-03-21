---
title: Gestire le risorse e le quote
titleSuffix: Azure Purview
description: Informazioni sulle quote e sui limiti delle risorse per Azure e su come richiedere aumenti di quota.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: 57cb1c405e00acb346421d64190a71e9211d21ff
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96938835"
---
# <a name="manage-and-increase-quotas-for-resources-with-azure-purview"></a>Gestisci e aumenta le quote per le risorse con Azure competenza
 
Azure competenza è un servizio cloud per l'uso da parte degli utenti di dati. Si usa Azure per gestire centralmente la governance dei dati nei propri dati, che comprendono ambienti cloud e locali. Il servizio consente agli analisti aziendali di cercare i dati rilevanti usando termini aziendali significativi. Per aumentare i limiti fino al valore massimo consentito per la sottoscrizione, contattare il supporto tecnico.
 
## <a name="azure-purview-limits"></a>Limiti di Azure Purview
 
|**Risorsa**|  **Limite predefinito**  |**Limite Massimo**|
|---|---|---|
|Account di competenza per area, per tenant (tutte le sottoscrizioni combinate)|3|Contattare il supporto tecnico|
|VCore disponibili per l'analisi, per account *|160|160|
|Analisi simultanee, per account in un determinato punto. Il limite è basato sul tipo di origini dati analizzate *|5 | 10 |
|Tempo massimo per l'esecuzione di un'analisi|7 giorni|7 giorni|
|Chiamate API, per account|10 milioni di API al mese per 4 unità di capacità della piattaforma. <br>API 40M al mese per 16 unità di capacità delle dimensioni della piattaforma |10 milioni di API al mese per 4 unità di capacità della piattaforma. <br>API 40M al mese per 16 unità di capacità delle dimensioni della piattaforma|
|Archiviazione, per account|10 GB per 4 unità di capacità dimensione piattaforma. <br>40 GB per 16 unità di capacità dimensione piattaforma |10 GB per 4 unità di capacità dimensione piattaforma. <br> 40 GB per 16 unità di capacità dimensione piattaforma |
|Dimensioni degli asset per account|100 milioni di risorse fisiche |Contattare il supporto tecnico|
|Dimensioni massime di un asset in un catalogo|2 MB|2 MB|
|Lunghezza massima del nome e della classificazione di un asset|4 KB|4 KB|
|Lunghezza massima del nome e del valore della proprietà Asset|32 KB|32 KB|
|Lunghezza massima del nome e del valore dell'attributo di classificazione|32 KB|32 KB|
|Numero massimo di termini di glossario, per account|100.000|100.000|
 
* Gli scenari di runtime di integrazione self-hosted non rientrano nell'ambito dei limiti definiti nella tabella precedente. 
 
## <a name="next-steps"></a>Passaggi successivi
 
> [!div class="nextstepaction"]
>[Esercitazione: analizzare i dati con Azure competenza](tutorial-scan-data.md)

> [!div class="nextstepaction"]
>[Esercitazione: esplorare il home page e cercare un asset](tutorial-asset-search.md)

> [!div class="nextstepaction"]
>[Esercitazione: esplorare gli asset e visualizzarne la derivazione](tutorial-browse-and-view-lineage.md)
