---
title: Connettere i dati Cisco Meraki ad Azure Sentinel | Microsoft Docs
description: Informazioni su come usare il connettore Cisco Meraki per eseguire il pull dei log di Cisco Meraki in Sentinel di Azure. Visualizzare i dati Cisco Meraki nelle cartelle di lavoro, creare avvisi e migliorare l'analisi.
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
ms.openlocfilehash: dc2d2a0724f18a02a5b667eb1004963a326ec360
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101745990"
---
# <a name="connect-your-cisco-meraki-to-azure-sentinel"></a>Connettere Cisco Meraki ad Azure Sentinel

> [!IMPORTANT]
> Il connettore Cisco Meraki è attualmente in versione di **Anteprima**. Vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per le note legali aggiuntive che si applicano alle funzionalità di Azure disponibili in versione beta, in anteprima o non ancora rilasciate a livello generale.

Questo articolo illustra come connettere le appliance Cisco Meraki (MX/MS/MR) ad Azure Sentinel. Cisco Meraki Data Connector consente di connettere facilmente i log di Cisco Meraki con Azure Sentinel, visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. L'integrazione tra Cisco Meraki e Azure Sentinel usa un server syslog con l'agente di Log Analytics installato. USA anche un parser di log personalizzato basato su una funzione kusto.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre dell'autorizzazione di lettura e scrittura per l'area di lavoro di Azure Sentinel.

- La soluzione Cisco Meraki deve essere configurata per esportare i log tramite syslog.

## <a name="send-cisco-meraki-logs-to-azure-sentinel-via-the-syslog-agent"></a>Inviare i log di Cisco Meraki ad Azure Sentinel tramite l'agente syslog  

Configurare Cisco Meraki per l'invio dei messaggi syslog all'area di lavoro di Azure Sentinel tramite l'agente syslog.

1. Nel menu di navigazione di Azure Sentinel selezionare **connettori dati**.

1. Dalla raccolta di **connettori dati** selezionare il connettore **Cisco Meraki (anteprima)** e quindi aprire la **pagina del connettore**.

1. Seguire le istruzioni nella pagina del connettore **Cisco Meraki** :

    1. Installare e caricare l'agente per Linux

        - Scegliere una VM Linux di Azure o un computer Linux non Azure (fisico o virtuale).

    1. Configurare i log da raccogliere

        - Selezionare le funzionalità e i livelli di gravità nella **configurazione degli agenti dell'area di lavoro**.

    1. Configurare e connettere il dispositivo/i Cisco Meraki

        - Seguire [queste istruzioni](https://documentation.meraki.com/General_Administration/Monitoring_and_Reporting/Meraki_Device_Reporting_-_Syslog%2C_SNMP_and_API) per configurare il dispositivo o i dispositivi Cisco Meraki per l'invio di syslog. Per il server remoto, usare l'indirizzo IP del computer Linux in cui è stato installato l'agente Linux.

## <a name="validate-connectivity-and-find-your-data"></a>Convalidare la connettività e trovare i dati

Potrebbero essere necessari fino a 20 minuti prima che i log inizino a essere visualizzati in Sentinel di Azure. 

Una volta stabilita la connessione, i dati vengono visualizzati nei **log**, nella sezione relativa alla *gestione dei log* , nella tabella *syslog* .

Questo connettore dati dipende da un parser basato su una funzione kusto per funzionare come previsto. [Seguire questa procedura](https://aka.ms/sentinel-ciscomeraki-parser) per creare l'alias della funzione **CiscoMeraki** kusto. Quindi, è possibile digitare `CiscoMeraki` nella finestra query per eseguire una query sui dati.

Vedere la scheda **passaggi successivi** nella pagina Connector per alcuni esempi di query utili.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere Cisco Meraki ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.
