---
title: Cartelle di lavoro di Azure Sentinel di uso comune | Microsoft Docs
description: Informazioni sulle cartelle di lavoro più comunemente usate per usare le risorse di Azure Sentinel più diffuse.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 03/07/2021
ms.author: bagol
ms.openlocfilehash: a37501498a9222025860702a7f29dccc9abfc8f7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102450063"
---
# <a name="commonly-used-azure-sentinel-workbooks"></a>Cartelle di lavoro di Azure Sentinel di uso comune

La tabella seguente elenca le cartelle di lavoro predefinite di Azure Sentinel più comunemente usate.

Accedere alle cartelle di lavoro in Sentinel di Azure in cartelle di lavoro di **gestione delle minacce** a  >   sinistra e quindi cercare la cartella di lavoro che si vuole usare. Per altre informazioni, vedere [esercitazione: visualizzare e monitorare i dati](tutorial-monitor-your-data.md).

|Nome cartella di lavoro  |Descrizione  |
|---------|---------|
|**Efficienza Analytics**     |  Fornisce informazioni approfondite sull'efficacia delle regole di analisi che consentono di ottenere migliori prestazioni del SOC. <br><br>Per ulteriori informazioni, vedere [il Toolkit per Data-Driven SOC](https://techcommunity.microsoft.com/t5/azure-sentinel/the-toolkit-for-data-driven-socs/ba-p/2143152).|
|**Attività di Azure**     |     Fornisce informazioni dettagliate sull'attività di Azure dell'organizzazione analizzando e correlando tutte le operazioni e gli eventi degli utenti. <br><br>Per altre informazioni, vedere [controllo con i log attività di Azure](audit-sentinel-data.md#auditing-with-azure-activity-logs).    |
|**Log di controllo Azure AD**     |  USA Azure Active Directory log di controllo per fornire informazioni dettagliate sugli scenari di Azure AD. <br><br>Per altre informazioni, vedere  [Guida introduttiva: Introduzione a Sentinel di Azure](quickstart-get-visibility.md).     |
|**Log di controllo, attività e accesso Azure AD**     |   Fornisce informazioni dettagliate sui dati di controllo, attività e accesso Azure Active Directory con una cartella di lavoro. Questa cartella di lavoro può essere usata sia dalla sicurezza che dagli amministratori di Azure.      |
|**Log di accesso Azure AD**     | USA i registri di accesso Azure AD per fornire informazioni dettagliate sugli scenari di Azure AD.        |
|**Certificazione del modello di maturità Cybersecurity (CMMC)**     |   Fornisce un meccanismo per visualizzare le query di log allineate ai controlli CMMC nel portfolio Microsoft, incluse le offerte di sicurezza Microsoft, Office 365, teams, Intune, desktop virtuale di Windows e così via. <br><br>Per altre informazioni, vedere [cartella di lavoro Cybersecurity Maturity Model Certification (CMMC) in anteprima pubblica](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-cybersecurity-maturity-model-certification-cmmc/ba-p/2111184).|
|**Monitoraggio dello stato della raccolta dati**     |   Fornisce informazioni dettagliate sullo stato di inserimento dei dati dell'area di lavoro. Visualizzare i monitoraggi e rilevare le anomalie che consentono di determinare l'integrità della raccolta dei dati dell'area di lavoro.  <br><br>Per altre informazioni, vedere [monitorare l'integrità dei connettori dati con questa cartella di lavoro di Azure Sentinel](monitor-data-connector-health.md).    |
|**Analizzatore eventi**     |  Consente di esplorare, controllare e velocizzare l'analisi del registro eventi di Windows, inclusi tutti i dettagli e gli attributi dell'evento, ad esempio sicurezza, applicazione, sistema, configurazione, servizio directory, DNS e così via.       |
|**Exchange Online**     |Fornisce informazioni dettagliate su Microsoft Exchange Online tracciando e analizzando tutte le operazioni di Exchange e le attività dell'utente.         |
|**Identità e accesso**     |   Fornisce informazioni dettagliate sulle operazioni di identità e accesso nell'utilizzo dei prodotti Microsoft, tramite i registri di sicurezza che includono i log di controllo e accesso.     |
|**Panoramica dell'evento imprevisto**     |   Progettato per semplificare la valutazione e l'analisi fornendo informazioni dettagliate su un evento imprevisto, incluse informazioni generali, dati di entità, tempo di valutazione, tempo di mitigazione e commenti. <br><br>Per ulteriori informazioni, vedere [il Toolkit per Data-Driven SOC](https://techcommunity.microsoft.com/t5/azure-sentinel/the-toolkit-for-data-driven-socs/ba-p/2143152).      |
|**Analisi approfondite**     | Fornisce analisti con informazioni dettagliate su eventi imprevisti, segnalibri e dati di entità. Query comuni e visualizzazioni dettagliate possono aiutare gli analisti a esaminare le attività sospette.       |
|**Microsoft Cloud App Security-log di individuazione**     |   Fornisce informazioni dettagliate sulle app Cloud usate nell'organizzazione e approfondimenti sulle tendenze di utilizzo e sui dati di drill-down per utenti e applicazioni specifici.  <br><br>Per altre informazioni, vedere [connettere i dati da Microsoft cloud app Security](connect-cloud-app-security.md).|
|**MITRE ATT&cartella di lavoro CK**     |   Fornisce informazioni dettagliate su MITRE ATT&la copertura CK per Azure Sentinel.      |
|**Office 365**     |  Fornisce informazioni dettagliate su Office 365 tracciando e analizzando tutte le operazioni e le attività. Esegui il drill-down nei dati di SharePoint, OneDrive ed Exchange.       |
|**Avvisi di sicurezza**     |  Fornisce un dashboard degli avvisi di sicurezza per gli avvisi nell'ambiente Sentinel di Azure. <br><br>Per ulteriori informazioni, vedere [creazione automatica degli eventi imprevisti da avvisi di sicurezza Microsoft](create-incidents-from-alerts.md).      |
|**Efficienza delle operazioni di sicurezza**     |  Progettato per i responsabili di Security Operations Center (SOC) per visualizzare le metriche e le misure di efficienza complessiva relative alle prestazioni del team. <br><br>Per altre informazioni, vedere [gestire meglio il SoC con le metriche per gli eventi imprevisti](manage-soc-with-incident-metrics.md).  |
|**Intelligence per le minacce**     | Fornisce informazioni dettagliate sugli indicatori di minaccia, inclusi il tipo e la gravità delle minacce, le attività di minaccia nel tempo e la correlazione con altre origini dati, tra cui Office 365 e firewall.  <br><br>Per altre informazioni, vedere [importare Intelligence per le minacce in Sentinel di Azure](import-threat-intelligence.md).      |
|**Controllo area di lavoro**     |  Fornisce un report di controllo dell'area di lavoro che consente di comprendere le esecuzioni di query nell'area di lavoro.   <br><br>Per altre informazioni, vedere [Audit Azure Sentinel queries and Activities](audit-sentinel-data.md).  |
|     |         |

