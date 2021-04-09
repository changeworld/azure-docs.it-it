---
title: Connettere i log di diagnostica Azure Key Vault ad Azure Sentinel
description: Informazioni su come usare i criteri di Azure per connettere i log di diagnostica Azure Key Vault ad Azure Sentinel.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 03/07/2021
ms.author: yelevin
ms.openlocfilehash: 00e634ec62d1fda4b1f2763a8c69b8e950d54beb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102505189"
---
# <a name="connect-azure-key-vault-diagnostics-logs"></a>Connetti Azure Key Vault log di diagnostica

Azure Key Vault è un servizio cloud per archiviare e accedere in modo sicuro ai segreti. Un segreto è qualsiasi elemento a cui si vuole controllare rigorosamente l'accesso, ad esempio chiavi API, password, certificati o chiavi crittografiche.

Questo connettore consente di trasmettere i log di diagnostica Azure Key Vault in Azure Sentinel, consentendo di monitorare continuamente l'attività in tutte le istanze.

Altre informazioni sul [monitoraggio Azure Key Vault](../azure-monitor/insights/key-vault-insights-overview.md) e sui dati di [telemetria di Azure Key Vault diagnostici](../key-vault/general/logging.md).

## <a name="prerequisites"></a>Prerequisiti

Per inserire i log di Azure Key Vault in Sentinel di Azure:

- È necessario disporre delle autorizzazioni di lettura e scrittura per l'area di lavoro di Azure Sentinel.

- Per usare criteri di Azure per applicare un criterio di streaming di log a Azure Key Vault risorse, è necessario disporre del ruolo proprietario per l'ambito di assegnazione dei criteri.

## <a name="connect-to-azure-key-vault"></a>Connetti a Azure Key Vault

Questo connettore usa i criteri di Azure per applicare una singola configurazione di streaming di log di Azure Key Vault a una raccolta di istanze, definita come ambito. È possibile visualizzare i tipi di log inseriti dal Azure Key Vault sul lato sinistro della pagina Connector, in **tipi di dati**.

1. Dal menu di navigazione di Azure Sentinel selezionare **connettori dati**.

1. Selezionare **Azure Key Vault** dalla raccolta di connettori dati, quindi selezionare **Apri pagina connettore** nel riquadro di anteprima.

1. Nella sezione **configurazione** della pagina connettore espandere **Abilita log di diagnostica su Azure Key Vault**.

1. Selezionare il pulsante **Avvia assegnazione guidata criteri di Azure** .

    Si apre la procedura guidata di assegnazione dei criteri, pronta per la creazione di un nuovo criterio denominato **deploy-Configure diagnostic Settings per Azure Key Vault per log Analytics area di lavoro**.

    1. Nella scheda **nozioni di base** fare clic sul pulsante con i tre puntini sotto **ambito** per selezionare la sottoscrizione (e, facoltativamente, un gruppo di risorse). È anche possibile aggiungere una descrizione.

    1. Nella scheda **parametri** scegliere l'area di lavoro di Azure Sentinel dall'elenco a discesa **area di lavoro log Analytics** . I campi a discesa rimanenti rappresentano i tipi di log di diagnostica disponibili. Lasciare contrassegnato come "true" tutti i tipi di log che si desidera inserire.

    1. Per applicare i criteri alle risorse esistenti, selezionare la scheda **monitoraggio e aggiornamento** e contrassegnare la casella di controllo **Crea un'attività di correzione** .

    1. Nella scheda **Rivedi e crea** fare clic su **Crea**. I criteri sono ora assegnati all'ambito scelto.

> [!NOTE]
>
> Con questo particolare connettore dati, gli indicatori di stato della connettività (uno stripe di colori nella raccolta di connettori dati e nelle icone di connessione accanto ai nomi dei tipi di dati) vengono visualizzati come connessi (verde) solo se i dati sono stati *inseriti* in un determinato momento nelle ultime due settimane. Una volta passate due settimane senza inserimento dati, il connettore verrà visualizzato come disconnesso. Il momento in cui arrivano più dati, lo stato *connesso* restituirà.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere Azure Key Vault ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
