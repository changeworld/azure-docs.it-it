---
title: Connettere i dati di LinuxAudit di NXLog ad Azure Sentinel | Microsoft Docs
description: Informazioni su come usare NXLog LinuxAudit Data Connector per eseguire il pull dei log di LinuxAudit in Sentinel di Azure. Visualizzare i dati LinuxAudit nelle cartelle di lavoro, creare avvisi e migliorare l'analisi.
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
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: 2010b21a3cdb81f2b2aa4180f87857862cd02bf5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101700875"
---
# <a name="connect-your-nxlog-linuxaudit-to-azure-sentinel"></a>Connettere il LinuxAudit di NXLog ad Azure Sentinel

> [!IMPORTANT]
> Il connettore NXLog LinuxAudit è attualmente in versione di **Anteprima**. Vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per le note legali aggiuntive che si applicano alle funzionalità di Azure disponibili in versione beta, in anteprima o non ancora rilasciate a livello generale.

Il connettore [NXLog LinuxAudit](https://nxlog.co/documentation/nxlog-user-guide/im_linuxaudit.html) consente di esportare facilmente gli eventi di sicurezza Linux in Sentinel di Azure in tempo reale, fornendo informazioni approfondite sulle attività Domain Name Server nell'intera organizzazione. Il modulo NXLog LinuxAudit supporta le regole di controllo personalizzate e raccoglie i log senza controllo o altro software dello spazio utente. Gli indirizzi IP e gli ID gruppo/utente vengono risolti con i rispettivi nomi rendendo i log di [controllo di Linux](https://nxlog.co/documentation/nxlog-user-guide/linux-audit.html) più comprensibili agli analisti della sicurezza. L'integrazione tra NXLog LinuxAudit e Azure Sentinel viene facilitata tramite l'API REST.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="configure-and-connect-nxlog-linuxaudit"></a>Configurare e connettere NXLog LinuxAudit

NXLog può essere configurato per inviare eventi in formato JSON direttamente in Sentinel di Azure.

1. Nel portale di Azure Sentinel fare clic su **connettori dati** e selezionare connettore **NXLog LinuxAudit** .

1. Selezionare **Apri connettore pagina**.

1. Seguire le istruzioni dettagliate nell'argomento di integrazione della *Guida dell'utente di NXLog* [Microsoft Azure Sentinel](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) per configurare l'invio tramite l'API REST.

## <a name="find-your-data"></a>Trovare i dati

Una volta stabilita la connessione, i dati vengono visualizzati nei **log** sotto la sezione  **log personalizzati** , nella tabella *LinuxAudit_CL* .

## <a name="validate-connectivity"></a>Convalidare la connettività

Potrebbero essere necessari fino a 20 minuti prima che i log inizino a essere visualizzati in Log Analytics.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come usare NXLog LinuxAudit per inserire gli eventi di sicurezza Linux in Sentinel di Azure. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.
