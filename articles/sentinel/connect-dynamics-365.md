---
title: Connettere i log di Dynamics 365 ad Azure Sentinel | Microsoft Docs
description: Informazioni su come usare il connettore per le attività di Dynamics 365 Common Data Service (CDS) per fornire informazioni sulle attività di amministrazione, utente e supporto in corso.
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
ms.date: 12/13/2020
ms.author: yelevin
ms.openlocfilehash: 018368b6284cf39edec01f0a9a943b8ea15c85d0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98104182"
---
# <a name="connect-dynamics-365-activity-logs-to-azure-sentinel"></a>Connettere i log attività di Dynamics 365 ad Azure Sentinel

Il connettore per le attività di [Dynamics 365](/office365/servicedescriptions/microsoft-dynamics-365-online-service-description) Common Data Service (CDS) fornisce informazioni dettagliate sulle attività di amministrazione, utente e supporto, nonché sugli eventi di registrazione di Microsoft Social Engagement. Connettendo i log di Dynamics 365 CRM in Sentinel di Azure, è possibile visualizzare questi dati nelle cartelle di lavoro, usarli per creare avvisi personalizzati e usarli per migliorare il processo di analisi. Questo nuovo connettore di Azure Sentinel raccoglie i dati di Dynamics CDS dall'API di gestione di Office. Per altre informazioni sulle attività di Dynamics CDS controllate in Power Platform, vedere [abilitare e usare la registrazione delle attività](/power-platform/admin/enable-use-comprehensive-auditing).

> [!IMPORTANT]
>
> Il connettore per le attività di Dynamics 365 Common Data Service (CDS) è attualmente in fase di **Anteprima**. Vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per le note legali aggiuntive che si applicano alle funzionalità di Azure disponibili in versione beta, in anteprima o non ancora rilasciate a livello generale.

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre delle autorizzazioni di lettura e scrittura per l'area di lavoro di Azure Sentinel.

- È necessario disporre di una [licenza Microsoft Dynamics 365 Production](/office365/servicedescriptions/microsoft-dynamics-365-online-service-description). Questo connettore non è disponibile per gli ambienti sandbox.
    - Per eseguire la registrazione delle attività, è necessaria una sottoscrizione di Microsoft 365 Enterprise [E3 o E5](/power-platform/admin/enable-use-comprehensive-auditing#requirements) .

- Per eseguire il pull dei dati dall'API di gestione di Office:
    - È necessario essere un amministratore globale nel tenant.

    - È necessario abilitare la [registrazione di controllo di Office](/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center) in [Centro sicurezza e conformità di Office](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance).

## <a name="enable-the-dynamics-365-activities-data-connector"></a>Abilitare il connettore dati di Dynamics 365 Activities

1. Dal menu di navigazione di Azure Sentinel selezionare **connettori dati**.

1. Dalla raccolta di **connettori dati** selezionare **Dynamics 365 (anteprima)** e quindi selezionare **Apri pagina connettore** nel riquadro di anteprima.

1. Nella scheda **istruzioni** , in **configurazione**, fare clic su **Connetti**. 

    Una volta attivato il connettore, saranno necessari circa 30 minuti fino a quando non sarà possibile visualizzare i dati in arrivo nel grafico. 

    In base al [log di controllo di Office nel centro di conformità](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance#requirements-to-search-the-audit-log), possono essere necessari fino a 30 minuti o fino a 24 ore dopo il verificarsi di un evento per la restituzione del record del log di controllo corrispondente nei risultati.

1. I log di controllo di Microsoft Dynamics sono reperibili nella `Dynamics365Activity` tabella. Vedere le informazioni di [riferimento sullo schema](/azure/azure-monitor/reference/tables/dynamics365activity)della tabella.

## <a name="querying-the-data"></a>Eseguire query sui dati

1. Nel menu di navigazione di Azure Sentinel selezionare **logs**.

1. Eseguire la query seguente per verificare l'arrivo dei log:

    ```kusto
    Dynamics365Activity
    | take 10
    ```


## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere i dati delle attività di Dynamics 365 ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Inizia a rilevare le minacce con Azure Sentinel, usando regole [predefinite](tutorial-detect-threats-built-in.md) o [personalizzate](tutorial-detect-threats-custom.md) .
