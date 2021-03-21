---
title: Connettere i log di diagnostica di Azure Kubernetes Service (AKS) ad Azure Sentinel
description: Informazioni su come usare i criteri di Azure per connettere i log di diagnostica del servizio Kubernetes di Azure ad Azure Sentinel.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 03/07/2021
ms.author: yelevin
ms.openlocfilehash: c3a4593aa92acededf9784974b2a1e2dd3cfb319
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102507178"
---
# <a name="connect-azure-kubernetes-service-diagnostics-logs"></a>Connettere i log di diagnostica del servizio Kubernetes di Azure

Azure Kubernetes Service (AKS) è un servizio di orchestrazione di contenitori open source completamente gestito che consente di distribuire, ridimensionare e gestire contenitori Docker e applicazioni basate su contenitori in un ambiente cluster.

Questo connettore consente di trasmettere i log di diagnostica di Azure Kubernetes Service (AKS) ad Azure Sentinel, consentendo di monitorare continuamente l'attività in tutte le istanze. 

Altre informazioni sul [monitoraggio del servizio Azure Kubernetes](../azure-monitor/containers/container-insights-overview.md) e sui dati di [telemetria relativi alla diagnostica AKS](../aks/view-control-plane-logs.md).

## <a name="prerequisites"></a>Prerequisiti

Per inserire i log AKS in Sentinel di Azure:

- È necessario disporre delle autorizzazioni di lettura e scrittura per l'area di lavoro di Azure Sentinel.

- Per usare criteri di Azure per applicare un criterio di streaming di log alle risorse AKS, è necessario disporre del ruolo proprietario per l'ambito di assegnazione dei criteri.

## <a name="connect-to-azure-kubernetes-service"></a>Connettersi al servizio Azure Kubernetes

Questo connettore usa i criteri di Azure per applicare una singola configurazione di flusso di log del servizio Kubernetes di Azure a una raccolta di istanze, definita come ambito. È possibile visualizzare i tipi di log inseriti dal servizio Kubernetes di Azure nella parte sinistra della pagina del connettore, in **tipi di dati**.

1. Dal menu di navigazione di Azure Sentinel selezionare **connettori dati**.

1. Selezionare **Azure Kubernetes Service (AKS)** dalla raccolta Data Connector e quindi selezionare **Apri pagina connettore** nel riquadro di anteprima.

1. Nella sezione **configurazione** della pagina connettore espandere **Abilita log di diagnostica in Azure KUBERNETES Service (AKS)**.

1. Selezionare il pulsante **Avvia assegnazione guidata criteri di Azure** .

    Si apre la procedura guidata di assegnazione dei criteri, pronta per la creazione di un nuovo criterio denominato **deploy-Configure diagnostic Settings for Azure Kubernetes Service to log Analytics area di lavoro**.

    1. Nella scheda **nozioni di base** fare clic sul pulsante con i tre puntini sotto **ambito** per selezionare la sottoscrizione (e, facoltativamente, un gruppo di risorse). È anche possibile aggiungere una descrizione.

    1. Nella scheda **parametri** scegliere l'area di lavoro di Azure Sentinel dall'elenco a discesa **area di lavoro log Analytics** . I campi a discesa rimanenti rappresentano i tipi di log di diagnostica disponibili. Lasciare contrassegnato come "true" tutti i tipi di log che si desidera inserire.

    1. Per applicare i criteri alle risorse esistenti, selezionare la scheda **monitoraggio e aggiornamento** e contrassegnare la casella di controllo **Crea un'attività di correzione** .

    1. Nella scheda **Rivedi e crea** fare clic su **Crea**. I criteri sono ora assegnati all'ambito scelto.

> [!NOTE]
>
> Con questo particolare connettore dati, gli indicatori di stato della connettività (uno stripe di colori nella raccolta di connettori dati e nelle icone di connessione accanto ai nomi dei tipi di dati) vengono visualizzati come connessi (verde) solo se i dati sono stati *inseriti* in un determinato momento nelle ultime due settimane. Una volta passate due settimane senza inserimento dati, il connettore verrà visualizzato come disconnesso. Il momento in cui arrivano più dati, lo stato *connesso* restituirà.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere il servizio Azure Kubernetes ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
