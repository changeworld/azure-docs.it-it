---
title: Connettere Archiviazione di Azure log di diagnostica dell'account a Azure Sentinel
description: Informazioni su come usare Criteri di Azure per connettere Archiviazione di Azure log di diagnostica dell'account a Azure Sentinel.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 04/21/2021
ms.author: yelevin
ms.openlocfilehash: b4260d31b587f2a20d7bc9d4c4e3e6a0d225a416
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107879130"
---
# <a name="connect-azure-storage-account-diagnostics-logs"></a>Connettersi Archiviazione di Azure log di diagnostica dell'account

Archiviazione di Azure è una soluzione cloud per scenari moderni di archiviazione dei dati. Contiene tutti gli oggetti dati: BLOB, file, code, tabelle e dischi.

Questo connettore consente di trasmettere i log di diagnostica degli account Archiviazione di Azure in Azure Sentinel, consentendo di monitorare continuamente l'attività e rilevare le minacce alla sicurezza in tutte le risorse di archiviazione di Azure in tutta l'organizzazione.

Altre informazioni sul [monitoraggio Archiviazione di Azure account](../storage/common/storage-analytics-logging.md).

## <a name="prerequisites"></a>Prerequisiti

Per inserire Archiviazione di Azure log di diagnostica dell'account in Azure Sentinel:

- È necessario disporre delle autorizzazioni di lettura e scrittura per l'area Azure Sentinel lavoro.

- Per usare Criteri di Azure per applicare un criterio di streaming dei log alle Archiviazione di Azure, è necessario disporre del ruolo Proprietario per l'ambito di assegnazione dei criteri.

## <a name="connect-to-azure-storage-account"></a>Connettersi all'account di archiviazione di Azure

Questo connettore usa Criteri di Azure per applicare una singola configurazione di streaming dei log a una raccolta di risorse Archiviazione di Azure account, definite come ambito. È possibile visualizzare i tipi di Archiviazione di Azure log e metrica disponibili sul lato sinistro della pagina del connettore, in **Tipi di dati**.

1. Dal menu Azure Sentinel di spostamento selezionare **Connettori dati**.

1. Selezionare **Archiviazione di Azure account dalla** raccolta connettori dati e quindi selezionare Apri pagina **connettore** nel riquadro di anteprima.

1. Nella sezione **Configurazione** della pagina del connettore espandere Stream diagnostics logs from your Archiviazione di Azure Account at scale (Streaming **diagnostics logs from your Archiviazione di Azure Account at scale**).

1. Selezionare il **pulsante Avvia Criteri di Azure assegnazione guidata.**

    Verrà visualizzata la procedura guidata per l'assegnazione dei criteri, pronta per creare un nuovo criterio denominato Configurare le impostazioni di diagnostica per **gli account di archiviazione nell'area di lavoro Log Analytics**.

    1. Nella scheda **Informazioni di base** fare clic sul pulsante con i tre puntini di sospensione accanto a **Ambito** per selezionare la sottoscrizione e, facoltativamente, un gruppo di risorse. È anche possibile aggiungere una descrizione.

    1. Nella scheda **Parametri:**
        - Scegliere l'Azure Sentinel lavoro dall'elenco a discesa Area di **lavoro Log Analytics.**
        - Selezionare nell'elenco a discesa **Servizi** di archiviazione per la distribuzione i tipi di risorse di archiviazione (file, tabelle, code e così via) in cui si vogliono distribuire le impostazioni di diagnostica.
        - Lasciare i **campi Nome impostazione** e **Effetto** così come sono.
        - I campi a discesa rimanenti rappresentano i tipi di log di diagnostica e di metrica disponibili. Lasciare contrassegnati come "True" tutti i tipi da inserire.

    1. I passaggi precedenti applieranno i criteri a tutte le risorse di archiviazione future. Per applicare i criteri alle risorse  esistenti, selezionare la scheda Correzione e selezionare la **casella** di controllo Crea un'attività di correzione.

    1. Nella scheda **Rivedi e crea** fare clic su **Crea**. I criteri vengono ora assegnati all'ambito scelto.

> [!NOTE]
>
> Con questo particolare connettore dati, gli indicatori di stato della connettività (una striscia di colore  nella raccolta dei connettori dati e le icone di connessione accanto ai nomi dei tipi di dati) verranno visualizzati come connessi (verde) solo se i dati sono stati inseriti in un determinato momento negli ultimi 14 giorni. Una volta trascorsi 14 giorni senza inserimento dati, il connettore verrà visualizzato come disconnesso. Nel momento in cui passano altri dati, verrà *restituito* lo stato connesso.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere un account Archiviazione di Azure a Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce.](quickstart-get-visibility.md)
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
