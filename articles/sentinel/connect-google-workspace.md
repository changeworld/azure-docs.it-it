---
title: Connettere i dati di Google Workspace (G Suite) ad Azure Sentinel | Microsoft Docs
description: Informazioni su come usare il connettore dati di Google Workspace (G Suite) per inserire gli eventi di attività dell'area di lavoro di Google in Sentinel di Azure. Visualizzare i dati dell'area di lavoro di Google nelle cartelle di lavoro, creare avvisi e migliorare l'analisi.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2021
ms.author: yelevin
ms.openlocfilehash: 4ada570502d913283ba9ee4cc4c65b7bdd853935
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101745263"
---
# <a name="connect-your-google-workspace-to-azure-sentinel"></a>Connettere l'area di lavoro di Google ad Azure Sentinel

> [!IMPORTANT]
> Il connettore Google Workspace è attualmente in **Anteprima**. Vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per le note legali aggiuntive che si applicano alle funzionalità di Azure disponibili in versione beta, in anteprima o non ancora rilasciate a livello generale.

Il connettore dati di [Google Workspace (in precedenza G Suite)](https://workspace.google.com/) offre la possibilità di inserire gli eventi di attività dell'area di lavoro di Google in Sentinel di Azure tramite l'API REST. Il connettore garantisce la visibilità degli [eventi](https://developers.google.com/admin-sdk/reports/reference/rest/v1/activities) nel SoC, che consente di esaminare i potenziali rischi per la sicurezza, analizzare l'utilizzo della collaborazione da parte del team, diagnosticare i problemi di configurazione, tenere traccia dell'accesso e del momento, analizzare l'attività dell'amministratore, comprendere come gli utenti possono creare e condividere il contenuto ed esaminare altri eventi nella propria organizzazione.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="prerequisites"></a>Prerequisiti

Per raccogliere i dati dell'area di lavoro di Google, è necessario disporre delle autorizzazioni e delle credenziali seguenti:

- Autorizzazioni di lettura e scrittura per l'area di lavoro di Azure Sentinel.

- Autorizzazioni di lettura per le chiavi condivise per l'area di lavoro. [Altre informazioni sulle chiavi dell'area di lavoro](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

- Autorizzazioni di lettura e scrittura per le funzioni di Azure per creare un app per le funzioni. [Altre informazioni su funzioni di Azure](../azure-functions/index.yml).

- La credenziale **GooglePickleString** è obbligatoria per l'API REST. [Altre informazioni sull'API REST di Google](https://developers.google.com/admin-sdk/reports/v1/reference/activities). [Informazioni su come ottenere le credenziali](https://developers.google.com/admin-sdk/reports/v1/quickstart/python).

## <a name="configure-and-connect-google-workspace"></a>Configurare e connettere l'area di lavoro Google

Google Workspace può integrare ed esportare i log direttamente in Sentinel di Azure usando un app per le funzioni di Azure.

> [!NOTE]
> Questo connettore usa funzioni di Azure per connettersi all'API di Google report per eseguire il pull degli eventi di attività in Sentinel di Azure. Questo può comportare costi di inserimento dati aggiuntivi. Per informazioni dettagliate, vedere la pagina dei [prezzi di funzioni di Azure](https://azure.microsoft.com/pricing/details/functions/) .

1. Nel portale di Azure Sentinel fare clic su **connettori dati**. 

1. Selezionare **area di lavoro Google (G Suite) (anteprima)** dalla raccolta di connettori e selezionare **Apri pagina connettore**.

1. Seguire i passaggi descritti nella sezione **configurazione** della pagina del connettore.

## <a name="validate-connectivity-and-find-your-data"></a>Convalidare la connettività e trovare i dati

Potrebbero essere necessari fino a 20 minuti fino a quando non si è in grado di visualizzare i dati inseriti in Sentinel di Azure.

Una volta stabilita la connessione, i dati vengono visualizzati nei **log**, nella sezione **log personalizzati** , nelle tabelle seguenti:
- `GWorkspace_ReportsAPI_admin_CL`
- `GWorkspace_ReportsAPI_calendar_CL`
- `GWorkspace_ReportsAPI_drive_CL`
- `GWorkspace_ReportsAPI_login_CL`
- `GWorkspace_ReportsAPI_mobile_CL`
- `GWorkspace_ReportsAPI_token_CL`
- `GWorkspace_ReportsAPI_user_accounts_CL`

Questo connettore dati dipende da un parser basato su una funzione kusto per funzionare come previsto. [Seguire questa procedura](https://aka.ms/sentinel-GWorkspaceReportsAPI-parser) per creare l'alias **GWorkspaceActivityReports** kusto Functions.

Per alcune query di esempio utili, vedere la scheda **passaggi successivi** nella pagina del connettore.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere l'area di lavoro di Google ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.
