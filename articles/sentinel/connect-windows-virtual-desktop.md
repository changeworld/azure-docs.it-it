---
title: Connettere Desktop virtuale Windows a Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati di Desktop virtuale Windows Azure Sentinel.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2021
ms.author: bagol
ms.openlocfilehash: a835ea7b5e79ecc9b2d26dc6955984d0d0ff2906
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107380293"
---
# <a name="connect-windows-virtual-desktop-data-to-azure-sentinel"></a>Connettere i dati di Desktop virtuale Windows Azure Sentinel

Questo articolo descrive come monitorare gli ambienti WVD (Windows Virtual Desktop) usando Azure Sentinel.

Ad esempio, il monitoraggio degli ambienti Desktop virtuale Windows può consentire di offrire un lavoro più remoto usando desktop virtualizzati, mantenendo al tempo stesso la sicurezza dell'organizzazione.

## <a name="windows-virtual-desktop-data-in-azure-sentinel"></a>Dati di Desktop virtuale Windows in Azure Sentinel

I dati di Desktop virtuale Windows in Azure Sentinel includono i tipi seguenti:


|Data  |Descrizione  |
|---------|---------|
|**Log eventi di Windows**     |  I log eventi di Windows dall'ambiente WVD vengono trasmessi in un'area di lavoro Log Analytics abilitata per Azure Sentinel come i log eventi di Windows da altri computer Windows, all'esterno dell'ambiente WVD. <br><br>Installare l'agente di Log Analytics nel computer Windows e configurare i log eventi di Windows da inviare all'area di lavoro Log Analytics.<br><br>Per altre informazioni, vedere:<br>- [Installare l'agente di Log Analytics nei computer Windows](/azure/azure-monitor/agents/agent-windows)<br>- [Raccogliere le origini dati del log eventi di Windows con l'agente di Log Analytics](/azure/azure-monitor/agents/data-sources-windows-events)<br>- [Connettere gli eventi di sicurezza di Windows](connect-windows-security-events.md)       |
|**Avvisi di Microsoft Defender per endpoint (MDE)**     |  Per configurare MDE per Desktop virtuale Windows, usare la stessa procedura utilizzata per qualsiasi altro endpoint di Windows. <br><br>Per altre informazioni, vedere: <br>- [Configurare Microsoft Defender per la distribuzione di endpoint](/windows/security/threat-protection/microsoft-defender-atp/production-deployment)<br>- [Connettere i dati da Microsoft 365 Defender a Azure Sentinel](connect-microsoft-365-defender.md)       |
|**Diagnostica di Desktop virtuale Windows**     | Diagnostica desktop virtuale Windows è una funzionalità del servizio PaaS desktop virtuale Windows, che registra le informazioni ogni volta che un utente assegnato al ruolo Desktop virtuale Windows usa il servizio. <br><br>Ogni log contiene informazioni sul ruolo Desktop virtuale Windows coinvolto nell'attività, gli eventuali messaggi di errore visualizzati durante la sessione, le informazioni sul tenant e le informazioni utente. <br><br>La funzionalità di diagnostica crea log attività sia per le azioni utente che per le azioni amministrative. <br><br>Per altre informazioni, vedere [Usare Log Analytics per la funzionalità di diagnostica in Desktop virtuale Windows.](/azure/virtual-desktop/virtual-desktop-fall-2019/diagnostics-log-analytics-2019)        |
|     |         |

## <a name="connect-windows-virtual-desktop-data"></a>Connettere i dati di Desktop virtuale Windows

Per iniziare a inserire i dati di Desktop virtuale Windows Azure Sentinel, seguire le istruzioni riportate nella documentazione di Desktop virtuale Windows.

Per altre informazioni, vedere [Eseguire il push dei dati di Desktop virtuale Windows nell'area di lavoro Log Analytics.](/azure/virtual-desktop/diagnostics-log-analytics)

## <a name="find-your-data"></a>Trovare i dati

Dopo aver stabilito una connessione riuscita, eseguire query Azure Sentinel sui dati di Log Analytics.

Ad esempio, vedere query di esempio dalla documentazione [di Desktop virtuale Windows](/azure/virtual-desktop/diagnostics-log-analytics).


Azure Sentinel fornisce anche query incorporate nell'area **General**  >  **Logs**  >  **WINDOWS VIRTUAL DESKTOP (Log generali DESKTOP VIRTUALE WINDOWS):**

[![Query incorporate di Desktop virtuale Windows in Azure Sentinel. ](media/connect-windows-virtual-desktop/windows-virtual-desktop-queries.png)](media/connect-windows-virtual-desktop/windows-virtual-desktop-queries.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi


Per altre informazioni, vedere il [glossario Monitoraggio di Azure per Desktop virtuale Windows](/azure/virtual-desktop/azure-monitor-glossary).
