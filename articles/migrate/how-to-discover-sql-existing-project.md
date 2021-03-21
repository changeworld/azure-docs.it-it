---
title: Individuazione di istanze di SQL Server in un progetto di Azure Migrate esistente
description: Informazioni su come individuare le istanze di SQL Server in un progetto di Azure Migrate esistente.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: ca0052eebd8d3c8e80943ca8c0e0346216436800
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452765"
---
# <a name="discover-sql-server-instances-in-an-existing-project"></a>Individuare le istanze di SQL Server in un progetto esistente 

Questo articolo descrive come individuare SQL Server istanze e database in un progetto [Azure migrate](./migrate-services-overview.md) creato prima dell'anteprima della funzionalità di valutazione di SQL Azure.

L'individuazione SQL Server istanze e i database in esecuzione in computer locali consentono di identificare e adattare un percorso di migrazione ad Azure SQL. Il Azure Migrate Appliance esegue questa individuazione usando le credenziali di dominio o SQL Server le credenziali di autenticazione che hanno accesso alle istanze SQL Server e ai database in esecuzione nei server di destinazione. Questo processo di individuazione è senza agenti, ovvero non è installato alcun elemento nei server di destinazione.

> [!Note]
> L'individuazione e la valutazione di SQL Server istanze e database in esecuzione nell'ambiente VMware sono ora in anteprima. Per provare questa funzionalità, usare [**questo collegamento**](https://aka.ms/AzureMigrate/SQL) per creare un progetto nell'area **Australia orientale**. Se si dispone già di un progetto in Australia orientale e si vuole provare questa funzionalità, assicurarsi di aver completato i [**prerequisiti**](how-to-discover-sql-existing-project.md) descritti in questo articolo.

## <a name="before-you-start"></a>Prima di iniziare

- Assicurarsi di avere: 
    - Creazione di un [progetto Azure migrate](./create-manage-projects.md) prima dell'annuncio della funzionalità SQL assessment per la propria area
    - Aggiunta dello strumento [Azure migrate: individuazione e valutazione](./how-to-assess.md) a un progetto
- Esaminare i [requisiti e il supporto per l'individuazione delle app](./migrate-support-matrix-vmware.md#vmware-requirements).
-  Verificare che i server in cui è in esecuzione App-Discovery dispongano di PowerShell versione 2,0 o successiva e che siano installati gli strumenti VMware (successivi a 10.2.0).
- Verificare i [requisiti](./migrate-appliance.md) per la distribuzione di Azure migrate Appliance.
- Verificare di disporre dei [ruoli necessari](./create-manage-projects.md#verify-permissions) nella sottoscrizione per creare risorse.
- Assicurarsi che il dispositivo abbia accesso a Internet

## <a name="enable-discovery-of-sql-server-instances-and-databases"></a>Abilitare l'individuazione di istanze e database di SQL Server

1. Nel progetto Azure Migrate,
    - Selezionare **non abilitato** nel riquadro hub o   :::image type="content" source="./media/how-to-discover-sql-existing-project/hub-not-enabled.png" alt-text="Azure migrate riquadro Hub con individuazione SQL non abilitato":::
    - Selezionare **non abilitato** in nessuna voce nella pagina individuazione server nella colonna istanze SQL   :::image type="content" source="./media/how-to-discover-sql-existing-project/discovery-not-enabled.png" alt-text="Azure migrate pannello server individuati con individuazione SQL non abilitata":::
2. In individua SQL Server le istanze e i database seguono i passaggi necessari:
    - Selezionare **Aggiorna** per creare la risorsa necessaria.
        :::image type="content" source="./media/how-to-discover-sql-existing-project/discovery-upgrade-appliance.png" alt-text="Pulsante per aggiornare il dispositivo Azure Migrate":::
    - Verificare che i servizi in esecuzione nell'appliance siano aggiornati alle versioni più recenti. A tale scopo, avviare Gestione configurazione Appliance dal server appliance e selezionare Visualizza servizi Appliance dal pannello imposta prerequisiti.
        - Il dispositivo e i relativi componenti vengono aggiornati automaticamente :::image type="content" source="./media/how-to-discover-sql-existing-project/appliance-services-version.png" alt-text="controllare la versione dell'appliance":::
    - Nel pannello Manage credentials and Discovery Sources di gestione configurazione Appliance aggiungere il dominio o SQL Server credenziali di autenticazione con accesso sysadmin sull'istanza SQL Server e sui database da individuare. 
    È possibile sfruttare la funzionalità di mapping automatico delle credenziali dell'appliance oppure eseguire manualmente il mapping delle credenziali al rispettivo server come indicato [qui](/azure/migrate/tutorial-discover-vmware#start-continuous-discovery).
        
    Alcuni punti da notare:
    - Verificare che l'inventario software sia già abilitato o fornire le credenziali di dominio o non di dominio per abilitare lo stesso. Per individuare le istanze di SQL Server, è necessario eseguire l'inventario software.
    - Il dispositivo tenterà di convalidare le credenziali di dominio con AD, man mano che vengono aggiunte. Verificare che il server appliance disponga di una linea di rete per il server AD associato alle credenziali. Le credenziali associate all'autenticazione SQL Server non vengono convalidate. 

3. Una volta aggiunte le credenziali desiderate, selezionare Avvia individuazione per iniziare l'analisi.

> [!Note] 
>Consentire l'esecuzione dell'individuazione SQL per qualche tempo prima di creare valutazioni per Azure SQL. Se l'individuazione di SQL Server istanze e database non è consentita per il completamento, le rispettive istanze vengono contrassegnate come **sconosciute** nel report di valutazione.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come creare una [valutazione SQL di Azure](./how-to-create-azure-sql-assessment.md)
- Altre informazioni sulle [valutazioni SQL di Azure](./concepts-azure-sql-assessment-calculation.md)