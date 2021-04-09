---
title: Connettere i dati VMware ESXi ad Azure Sentinel | Microsoft Docs
description: Informazioni su come usare il connettore di dati VMware ESXi per eseguire il pull dei log ESXi in Sentinel di Azure. Visualizzare i dati ESXi nelle cartelle di lavoro, creare avvisi e migliorare l'analisi.
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
ms.date: 03/01/2021
ms.author: yelevin
ms.openlocfilehash: 3d478a9ac3cf91f3f6815859b8534efff88f07b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101700867"
---
# <a name="connect-your-vmware-esxi-to-azure-sentinel"></a>Connettere la VMware ESXi ad Azure Sentinel

> [!IMPORTANT]
> Il connettore VMware ESXi è attualmente in fase di **Anteprima**. Vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per le note legali aggiuntive che si applicano alle funzionalità di Azure disponibili in versione beta, in anteprima o non ancora rilasciate a livello generale.

Questo articolo illustra come connettere il dispositivo VMware ESXi ad Azure Sentinel. Il connettore VMware ESXi Data consente di inserire facilmente i log di VMware ESXi in Sentinel di Azure, offrendo più informazioni sulle attività ESXi dell'organizzazione e contribuendo a migliorare le funzionalità operative di sicurezza. L'integrazione tra VMware ESXi e Azure Sentinel usa un server syslog con l'agente di Log Analytics installato. USA anche un parser di log personalizzato basato su una funzione kusto.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre dell'autorizzazione di lettura e scrittura per l'area di lavoro di Azure Sentinel.

- La soluzione di VMware ESXi deve essere configurata per esportare i log tramite syslog.

## <a name="send-vmware-esxi-logs-to-the-syslog-agent"></a>Inviare i log di VMware ESXi all'agente syslog  

Configurare VMware ESXi per l'invio dei messaggi syslog all'area di lavoro di Azure Sentinel tramite l'agente syslog.
3. Seguire le istruzioni nella pagina **VMware ESXi** .


1. Nel menu di navigazione di Azure Sentinel selezionare **connettori dati**.

1. Dalla raccolta di **connettori dati** selezionare il connettore **VMware ESXi (anteprima)** , quindi aprire la **pagina del connettore**.

1. Seguire le istruzioni nella pagina **VMware ESXi** Connector:

    1. Installare e caricare l'agente per Linux

        - Scegliere una VM Linux di Azure o un computer Linux non Azure (fisico o virtuale).

    1. Configurare i log da raccogliere

        - Selezionare le funzionalità e i livelli di gravità nella **configurazione degli agenti dell'area di lavoro**.

    1. Configurare e connettere il VMware ESXi

        - Seguire queste istruzioni per configurare il VMware ESXi per l'invio di syslog:
            - [VMware ESXi 3,5 e 4. x](https://kb.vmware.com/s/article/1016621)
            - [VMware ESXi 5,0 e versioni successive](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.monitoring.doc/GUID-9F67DB52-F469-451F-B6C8-DAE8D95976E7.html)

            Per il server remoto, usare l'indirizzo IP del computer Linux in cui è stato installato l'agente Linux.

## <a name="validate-connectivity-and-find-your-data"></a>Convalidare la connettività e trovare i dati

Potrebbero essere necessari fino a 20 minuti prima che i log inizino a essere visualizzati in Sentinel di Azure. 

Una volta stabilita la connessione, i dati vengono visualizzati nei **log**, nella sezione relativa alla *gestione dei log* , nella tabella *syslog* .

Questo connettore dati dipende da un parser basato su una funzione kusto per funzionare come previsto. [Seguire questa procedura](https://aka.ms/sentinel-vmwareesxi-parser) per creare l'alias della funzione **VMwareESXi** kusto. Quindi, è possibile digitare `VMwareESXi` qualsiasi finestra di query in Sentinel di Azure per eseguire query sui dati.

Vedere la scheda **passaggi successivi** nella pagina Connector per alcuni esempi di query utili.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere VMware ESXi ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.
