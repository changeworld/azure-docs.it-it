---
title: Processo Web, attività in background, in Azure
description: Informazioni sui processi Web.
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 7714b090399b0b184e2e216ff6da7b10f2bf4386
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452272"
---
# <a name="webjobs-run-background-tasks-in-azure-app-service"></a>Processi Web: esecuzione di attività in background nel servizio app Azure

L'articolo descrive come distribuire Processi Web usando il [portale di Azure](https://portal.azure.com) per caricare un eseguibile o uno script. Per informazioni su come sviluppare e distribuire processi Web usando Visual Studio, vedere [Deploy WebJobs using Visual Studio](webjobs-dotnet-deploy-vs.md) (Distribuire processi Web usando Visual Studio).

## <a name="overview"></a>Panoramica
Processi Web è una funzionalità di [app Azure servizio](index.yml) che consente di eseguire un programma o uno script nella stessa istanza di un'app Web, un'app per le API o un'app per dispositivi mobili. Non sono previsti costi aggiuntivi per l'uso di Processi Web.

> [!IMPORTANT]
> Processi Web non è ancora supportato per il servizio App in Linux.

Azure WebJobs SDK può essere usato con Processi Web per semplificare diverse attività di programmazione. Per ulteriori informazioni, vedere [Definizione dell'SDK di Processi Web](https://github.com/Azure/azure-webjobs-sdk/wiki).

Funzioni di Azure consente di eseguire programmi e script in un altro modo. Per un confronto tra Processi Web e Funzioni, vedere [Scegliere tra Flow, App per la logica, Funzioni e Processi Web](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="webjob-types"></a>Tipi di processi Web

La tabella seguente descrive le differenze tra processi Web *continui* e *attivati*.


|Continua  |Attivato  |
|---------|---------|
| Viene avviato immediatamente quando il processo Web viene creato. Per evitare che il processo termini, il programma o lo script in genere opera in un ciclo infinito. Se il processo termina, è possibile riavviarlo. | Viene avviato solo se attivato manualmente o in base a una pianificazione. |
| Viene eseguito in tutte le istanze in cui viene eseguita l'app Web. È facoltativamente possibile limitare il processo Web a una sola istanza. |Viene eseguito in una singola istanza selezionata da Azure per il bilanciamento del carico.|
| Supporta il debug remoto. | Non supporta il debug remoto.|

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

## <a name="add-webjob-to-source-control"></a>Aggiungere processo Web al controllo del codice sorgente

Se è stato configurato il controllo del codice sorgente con l'applicazione, i processi Web devono essere distribuiti come parte dell'integrazione del controllo del codice sorgente. Una volta configurato il controllo del codice sorgente con l'applicazione, non è possibile aggiungere un processo Web dal portale di Azure.

## <a name="supported-file-types-for-scripts-or-programs"></a><a name="acceptablefiles"></a>Tipi di file supportati per script e programmi

Sono supportati i tipi di file seguenti:

* .cmd, .bat, .exe (prompt dei comandi di Windows)
* .ps1 (PowerShell)
* .sh (Bash)
* .php (PHP)
* .py (Python)
* .js (Node.js)
* .jar (Java)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [creare un processo Web](./webjobs-create-ieux.md)
* Visualizzare la cronologia dei log dei [processi](./webjobs-create-ieux-view-log.md) Web
