---
title: Visualizzare la cronologia dei log dei processi Web
description: Visualizzare la cronologia dei log per i processi non riusciti e riusciti.
author: ggailey777
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 4b4e421458438aecc7c08e397f1fc919a8cc2225
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101745675"
---
# <a name="view-webjob-history-in-the-azure-portal"></a>Visualizza la cronologia processo Web nel portale di Azure

Visualizzare la cronologia dei log per i processi non riusciti e riusciti.

1. Selezionare il processo Web di cui si vuole visualizzare la cronologia e quindi selezionare il pulsante **Log**.

    ![Pulsante Log](./media/web-sites-create-web-jobs/wjbladelogslink.png)

1. Nella pagina **WebJob Details** (Dettagli processo Web) selezionare un'ora per visualizzare i dettagli di un'esecuzione.

    ![Dettagli del processo Web](./media/web-sites-create-web-jobs/webjobdetails.png)

1. Nella pagina **WebJob Run Details** (Dettagli esecuzione processo Web) selezionare **Toggle Output** (Attiva/Disattiva output) per visualizzare il testo dei contenuti del log.

    ![Dettagli dell'esecuzione del processo Web](./media/web-sites-create-web-jobs/webjobrundetails.png)

    Per visualizzare il testo dell'output in una finestra del browser separata, selezionare **download**. Per scaricare il testo stesso, fare clic con il pulsante destro del mouse su **download** e usare le opzioni del browser in uso per salvare il contenuto del file.

1. Selezionare il collegamento del percorso **Processi Web** nella parte superiore della pagina per passare a un elenco di processi Web.

    ![Percorso Processi Web](./media/web-sites-create-web-jobs/breadcrumb.png)

    ![Elenco dei processi nel dashboard della cronologia](./media/web-sites-create-web-jobs/webjobslist.png)

## <a name="next-steps"></a>Passaggi successivi

* Usare [Webjobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) per semplificare molte attività di programmazione

* Informazioni su come [sviluppare e distribuire processi Web con Visual Studio](webjobs-dotnet-deploy-vs.md)