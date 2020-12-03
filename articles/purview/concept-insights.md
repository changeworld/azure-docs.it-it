---
title: Informazioni sui report di Insights in Azure
description: Questo articolo illustra le informazioni dettagliate disponibili in Azure.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: d841fa336b2702a02f3215f97a6403217986d7e0
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96554764"
---
# <a name="understand-insights-in-azure-purview"></a>Informazioni dettagliate su Azure

Questo articolo fornisce una panoramica della funzionalità Insights in Azure.

Le informazioni dettagliate sono uno dei pilastri chiave di competenza. La funzionalità fornisce ai clienti, un unico riquadro di visualizzazione nel catalogo e mira a fornire informazioni dettagliate specifiche per gli amministratori delle origini dati, gli utenti aziendali, gli amministratori dei dati, gli amministratori dei dati e gli amministratori della sicurezza. Attualmente, per ambito sono disponibili i report Insights seguenti che saranno disponibili per i clienti in anteprima pubblica.

## <a name="asset-insights"></a>Informazioni dettagliate su asset

Questo report fornisce una panoramica del patrimonio di dati e la relativa distribuzione in base al tipo di origine, alla classificazione e alle dimensioni dei file come alcune delle dimensioni. Questo report soddisfa i diversi tipi di utenti che possono gestire l'account di competenza e l'esecuzione di analisi o utenti aziendali che potrebbero essere interessati a conoscere il numero di asset esistenti con una determinata classificazione all'interno dell'azienda. 

Il report fornisce informazioni approfondite attraverso grafici e indicatori KPI e successivamente approfondisce le anomalie specifiche, ad esempio i file posizionati in modo non consentiti. Il report supporta inoltre un'esperienza utente end-to-end, in cui il cliente può visualizzare il numero di asset con una classificazione specifica, può scomporre le informazioni in base ai tipi di origine e alle cartelle principali e può anche visualizzare l'elenco degli asset per un'analisi più approfondita.

## <a name="scan-insights"></a>Analizza informazioni dettagliate

Il report consente agli amministratori di comprendere l'integrità complessiva delle analisi, il numero di riuscite, il numero di errori, il numero di annullati. Questo report fornisce un aggiornamento dello stato delle analisi eseguite nell'account di competenza entro un periodo di tempo di ultimi sette giorni o ultimi 30 giorni.

Il report consente inoltre agli amministratori di approfondire ed esplorare le analisi non riuscite e i tipi di origine specifici. Per consentire agli utenti di esaminare ulteriormente, il report consente di spostarsi nella pagina Cronologia analisi all'interno dell'esperienza "origini".

## <a name="glossary-insights"></a>Informazioni dettagliate sul glossario

Questo report fornisce agli utenti aziendali e ai dati un rapporto di stato sul glossario. Gli utenti possono visualizzare questo report per comprendere la distribuzione dei termini di glossario in base allo stato, scoprire quanti termini di glossario sono collegati agli asset e quanti non sono ancora collegati ad alcun asset. Gli utenti aziendali possono inoltre apprendere informazioni sulla completezza dei termini di glossario. 

Questo report riepiloga gli elementi principali di cui un utente aziendale o un amministratore dei dati deve concentrarsi, per creare un glossario completo e utilizzabile per la propria organizzazione. Gli utenti possono anche passare all'esperienza "Glossario" dall'esperienza "Glossario Insights" per apportare modifiche in base a un termine specifico del glossario.

## <a name="classification-insights"></a>Informazioni dettagliate sulla classificazione

Questo report fornisce informazioni dettagliate su dove si trovano i dati classificati, sulle classificazioni individuate durante un'analisi e su un drill-down per i file classificati. Consente agli amministratori della sicurezza di comprendere i tipi di informazioni presenti nel relativo patrimonio di dati dell'organizzazione. 

In Azure, le classificazioni sono simili ai tag Subject e vengono usate per contrassegnare e identificare il contenuto di un tipo specifico nel proprio data.

Usare il report informazioni dettagliate di classificazione per identificare il contenuto con classificazioni specifiche e comprendere le azioni necessarie, ad esempio l'aggiunta di ulteriore sicurezza ai repository o lo spostamento di contenuto in una posizione più sicura.

Per ulteriori informazioni, vedere informazioni [dettagliate sulla classificazione dei dati da Azure](classification-insights.md).

## <a name="sensitivity-labeling-insights"></a>Informazioni dettagliate sulle etichette di riservatezza

Questo report fornisce informazioni dettagliate sulle etichette di riservatezza trovate durante un'analisi, oltre a un drill-down per i file con etichetta. Consente agli amministratori della sicurezza di garantire la sicurezza delle informazioni rilevate nel relativo patrimonio di dati aziendale. 

In Azure, le etichette di riservatezza vengono usate per identificare le categorie dei tipi di classificazione, nonché i criteri di sicurezza del gruppo che si desidera applicare a ogni categoria.

Usare il report delle informazioni dettagliate di etichettatura per identificare le etichette di riservatezza trovate nel contenuto e comprendere le azioni necessarie, ad esempio la gestione dell'accesso a repository o file specifici.

Per altre informazioni, vedere informazioni [dettagliate sull'etichetta di riservatezza sui dati in Azure](sensitivity-insights.md).

## <a name="file-extension-insights"></a>Informazioni dettagliate sull'estensione di file

Questo report fornisce informazioni dettagliate sulle estensioni di file o sui tipi di file, individuati durante un'analisi, nonché un drill-down per i file stessi. 

Usare il report delle informazioni dettagliate sull'estensione di file per comprendere il numero di file di ogni volta, dove si trovano i file e se sono analizzabili per i dati sensibili.

Per altre informazioni, vedere informazioni [dettagliate sull'estensione di file per i dati di Azure](file-extension-insights.md).

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni dettagliate sul glossario](glossary-insights.md)
* [Analizza informazioni dettagliate](scan-insights.md)
* [Informazioni dettagliate sulla classificazione](./classification-insights.md)