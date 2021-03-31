---
title: Come vengono rilevati gli asset eliminati tramite le analisi
description: Questo articolo illustra il modo in cui un account Azure di competenza rileva gli asset eliminati durante le analisi.
author: yaronyg
ms.author: yarong
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: 9b1515ef00355c831e161c01227678197792cc20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96555388"
---
# <a name="how-scans-detect-deleted-assets"></a>Come vengono rilevati gli asset eliminati tramite le analisi

Questo articolo descrive il modo in cui Azure di competenza usa i risultati dell'analisi per rilevare gli asset eliminati.

## <a name="background-info"></a>Informazioni di base

Un catalogo di competenze di Azure è in grado di riconoscere lo stato di un archivio dati solo quando esegue l'analisi. Per verificare se un file, una tabella o un contenitore è stato eliminato, il catalogo confronta l'output dell'ultima analisi con l'output di analisi corrente. Si supponga, ad esempio, che l'ultima volta che è stata eseguita la scansione di un account Azure Data Lake Storage Gen2, inclusa una cartella denominata *folder1*. Quando lo stesso account viene nuovamente analizzato, *folder1* è mancante. Pertanto, il catalogo presuppone che la cartella sia stata eliminata.

## <a name="detecting-deleted-files"></a>Rilevamento dei file eliminati

La logica per il rilevamento di file mancanti funziona per più analisi dallo stesso utente e da utenti diversi. Si supponga, ad esempio, che un utente esegua un'analisi monouso in un archivio dati Data Lake Storage Gen2 sulle cartelle A, B e C. In un secondo momento, un altro utente nello stesso account esegue un'analisi singola per le cartelle C, D ed e dello stesso archivio dati. Poiché la cartella C è stata analizzata due volte, il catalogo controlla la presenza di possibili eliminazioni. Le cartelle A, B, D ed e, tuttavia, sono state analizzate una sola volta e il catalogo non verificherà gli asset eliminati.

Per evitare che i file vengano eliminati dal catalogo, è importante eseguire analisi regolari. L'intervallo di analisi è importante, perché il catalogo non può rilevare le risorse eliminate fino a quando non viene eseguita un'altra analisi. Quindi, se si eseguono le analisi una volta al mese in un determinato negozio, il catalogo non è in grado di rilevare gli asset di dati eliminati nell'archivio fino a quando non si esegue l'analisi successiva al mese successivo.

Quando si enumerano archivi dati di grandi dimensioni, ad esempio Data Lake Storage Gen2, esistono diversi modi, ad esempio errori di enumerazione ed eventi eliminati, per perdere le informazioni. Una particolare analisi potrebbe non comportare la creazione o l'eliminazione di un file. Quindi, a meno che il catalogo non sia certo che un file è stato eliminato, non lo eliminerà dal catalogo. Questa strategia indica che possono esserci errori quando un file che non esiste nell'archivio dati scansionato esiste ancora nel catalogo. In alcuni casi, potrebbe essere necessario eseguire l'analisi di un archivio dati due o tre volte prima di intercettare determinati asset eliminati.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare i cataloghi di Azure per le competenze, vedere [Guida introduttiva: creare un account di competenza](create-catalog-portal.md).
