---
title: Connettere i log di diagnostica e di controllo del database SQL di Azure ad Azure Sentinel
description: Informazioni su come connettere i log di diagnostica del database SQL di Azure e i log di controllo di sicurezza a Sentinel di Azure.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 01/06/2021
ms.author: yelevin
ms.openlocfilehash: df132c35ebb04596d91720431f5b08cb88e2abd9
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2021
ms.locfileid: "98104190"
---
# <a name="connect-azure-sql-database-diagnostics-and-auditing-logs"></a>Connettere i log di diagnostica e di controllo del database SQL di Azure

Azure SQL è un motore di database PaaS (Platform-as-a-Service) completamente gestito che gestisce la maggior parte delle funzioni di gestione di database, ad esempio l'aggiornamento, l'applicazione di patch, i backup e il monitoraggio, senza coinvolgimento degli utenti. 

Il connettore del database SQL di Azure consente di trasmettere i log di diagnostica e di controllo dei database in Sentinel, consentendo di monitorare continuamente l'attività in tutte le istanze.

- La connessione dei log di diagnostica consente di inviare log di diagnostica del database di tipi di dati diversi all'area di lavoro di Sentinel.

- La connessione dei log di controllo consente di eseguire lo streaming dei log di controllo di sicurezza da tutti i database SQL di Azure a livello di server.

Altre informazioni sul [monitoraggio dei database SQL di Azure](../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md).

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre delle autorizzazioni di lettura e scrittura per l'area di lavoro di Azure Sentinel.

- Per connettere i log di controllo, è necessario disporre delle autorizzazioni di lettura e scrittura per le impostazioni di controllo di Azure SQL Server.

## <a name="connect-to-azure-sql-database"></a>Connettersi al database SQL di Azure
    
1. Dal menu di navigazione di Azure Sentinel selezionare **connettori dati**.

1. Selezionare **database SQL di Azure** dalla raccolta Data Connector e quindi selezionare **Apri pagina connettore**  nel riquadro di anteprima.

1. Nella sezione **configurazione** della pagina connettore prendere nota delle due categorie di log che è possibile connettere.

### <a name="connect-diagnostics-logs"></a>Connettere i log di diagnostica

1. In **log di diagnostica** espandere **Abilita manualmente i log di diagnostica in ogni database SQL di Azure**.

1. Selezionare il collegamento **apri >SQL di Azure** per aprire il pannello risorse **SQL di Azure** .

1. **(Facoltativo)** Per trovare facilmente la risorsa di database, selezionare **Aggiungi filtro** sulla barra dei filtri nella parte superiore.
    1. Nell'elenco a discesa **filtro** selezionare **tipo di risorsa**.
    1. Nell'elenco a discesa **valore** **deselezionare Seleziona tutto**, quindi selezionare **database SQL**.
    1. Fare clic su **Applica**.
    
1. Selezionare la risorsa di database di cui si vogliono inviare i log di diagnostica a Sentinel di Azure.

    > [!NOTE]
    > Per ogni risorsa di database di cui si desidera raccogliere i log, è necessario ripetere questo processo, a partire da questo passaggio.

1. Dalla pagina delle risorse del database selezionato, in **monitoraggio** nel menu di navigazione selezionare **impostazioni di diagnostica**.

    1. Selezionare il collegamento **+ Aggiungi impostazione di diagnostica** nella parte inferiore della tabella.

    1. Nella schermata **impostazioni di diagnostica** immettere un nome nel campo  **Nome impostazione di diagnostica** .
    
    1. Nella colonna **Dettagli destinazione** contrassegnare la casella **di controllo Invia a log Analytics area di lavoro** . Sotto di essa verranno visualizzati due nuovi campi. Scegliere la **sottoscrizione** pertinente e l' **area di lavoro log Analytics** (in cui risiede Azure Sentinel).

    1. Nella colonna **Dettagli categoria** selezionare le caselle di controllo dei tipi di log e metrica che si desidera inserire. Si consiglia di selezionare tutti i tipi disponibili in **log** e **metrica**.

    1. Selezionare **Save (Salva** ) nella parte superiore della schermata.

- In alternativa, è possibile usare lo **script di PowerShell** fornito per connettere i log di diagnostica.
    1. In **log di diagnostica** espandere **Abilita in base allo script di PowerShell**.

    1. Copiare il blocco di codice e incollarlo in PowerShell.

### <a name="connect-audit-logs"></a>Connettere i log di controllo

1. In **log di controllo (anteprima)** espandere **Abilita i log di controllo in tutti i database SQL di Azure (a livello di server)**.

1. Selezionare il collegamento **apri >SQL di Azure** per aprire il pannello della risorsa **SQL Server** .

1. Selezionare il server SQL di cui si vogliono inviare i log di controllo da inviare a Sentinel di Azure.

    > [!NOTE]
    > Per ogni risorsa server di cui si vogliono raccogliere i log, è necessario ripetere questo processo, a partire da questo passaggio.

1. Dalla pagina delle risorse del server selezionato, in **sicurezza** nel menu di navigazione selezionare **controllo**.

    1. Spostare l'interruttore **Abilita controllo SQL di Azure** **su on**.

    1. In **destinazione log di controllo** selezionare **log Analytics (anteprima)**.
    
    1. Nell'elenco delle aree di lavoro visualizzato scegliere l'area di lavoro (in cui risiede Azure Sentinel).

    1. Selezionare **Save (Salva** ) nella parte superiore della schermata.

- In alternativa, è possibile usare lo **script di PowerShell** fornito per connettere i log di diagnostica.
    1. In **log di controllo** espandere **Abilita in base allo script di PowerShell**.

    1. Copiare il blocco di codice e incollarlo in PowerShell.


> [!NOTE]
>
> Con questo particolare connettore dati, gli indicatori di stato della connettività (uno stripe di colori nella raccolta di connettori dati e nelle icone di connessione accanto ai nomi dei tipi di dati) vengono visualizzati come connessi (verde) solo se i dati sono stati *inseriti* in un determinato momento nelle ultime due settimane. Una volta passate due settimane senza inserimento dati, il connettore verrà visualizzato come disconnesso. Il momento in cui arrivano più dati, lo stato *connesso* restituirà.

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere i log di diagnostica e di controllo del database SQL di Azure a Sentinel di Azure. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).