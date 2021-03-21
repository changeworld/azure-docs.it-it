---
title: Individuare le app nei server locali con Azure Migrate
description: Informazioni su come individuare app, ruoli e funzionalità nei server locali con Azure Migrate Assessment server.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/10/2020
ms.openlocfilehash: 8266b585881546b37bbb21b82780ab26d85dada7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048081"
---
# <a name="discover-installed-applications-roles-and-features-software-inventory-and-sql-server-instances-and-databases"></a>Individuare le applicazioni, i ruoli e le funzionalità installate (inventario software) e i database e le istanze di SQL Server

Questo articolo descrive come individuare le applicazioni, i ruoli e le funzionalità installate (inventario software) e SQL Server istanze e database nei server in esecuzione nell'ambiente VMware, usando Azure Migrate: strumento di valutazione del server.

L'inventario software consente di identificare e personalizzare un percorso di migrazione ad Azure per i carichi di lavoro. L'inventario software usa il dispositivo Azure Migrate per eseguire l'individuazione, usando le credenziali del server. È completamente senza agenti. per raccogliere questi dati, non vengono installati agenti nei server.

> [!NOTE]
> L'inventario software è attualmente disponibile in anteprima per i server in esecuzione solo nell'ambiente VMware ed è limitato all'individuazione. Attualmente non è disponibile una valutazione basata su applicazioni.<br/> L'individuazione e la valutazione di SQL Server istanze e database in esecuzione nell'ambiente VMware sono ora in anteprima. Per provare questa funzionalità, usare [**questo collegamento**](https://aka.ms/AzureMigrate/SQL) per creare un progetto nell'area **Australia orientale**. Se si dispone già di un progetto nell'Australia orientale e si vuole provare questa funzionalità, assicurarsi di aver completato questi [**prerequisiti**](how-to-discover-sql-existing-project.md) nel portale.

## <a name="before-you-start"></a>Prima di iniziare

- Assicurarsi di aver [creato un progetto Azure migrate](./create-manage-projects.md) con lo strumento Azure migrate: server assessment.
- Esaminare i [requisiti di VMware](migrate-support-matrix-vmware.md#vmware-requirements) per eseguire l'inventario software.
- Esaminare i [requisiti dell'appliance](migrate-support-matrix-vmware.md#azure-migrate-appliance-requirements) prima di configurare l'appliance.
- Esaminare i [requisiti di individuazione delle applicazioni](migrate-support-matrix-vmware.md#application-discovery-requirements) prima di avviare l'inventario software nei server.

## <a name="deploy-and-configure-the-azure-migrate-appliance"></a>Distribuire e configurare il dispositivo Azure Migrate

1. [Esaminare](migrate-appliance.md#appliance---vmware) i requisiti per la distribuzione di Azure migrate Appliance.
2. Esaminare gli URL di Azure a cui l'appliance dovrà accedere nei cloud [pubblici](migrate-appliance.md#public-cloud-urls) e [governativi](migrate-appliance.md#government-cloud-urls).
3. [Esaminare i dati](migrate-appliance.md#collected-data---vmware) che l'appliance raccoglierà durante l'individuazione e la valutazione.
4. [Prendere nota](migrate-support-matrix-vmware.md#port-access-requirements) dei requisiti di accesso alle porte per l'appliance.
5. [Distribuire il dispositivo Azure migrate](how-to-set-up-appliance-vmware.md) per avviare l'individuazione. Per distribuire l'appliance, scaricare e importare un modello OVA in VMware per creare un server in esecuzione nel server vCenter. Dopo aver distribuito il dispositivo, è necessario registrarlo con il progetto Azure Migrate e configurarlo per avviare l'individuazione.
6. Quando si configura l'appliance, è necessario specificare quanto segue in Gestione configurazione Appliance:
    - Dettagli del server vCenter a cui si desidera connettersi.
    - server vCenter le credenziali con ambito per individuare i server nell'ambiente VMware.
    - Credenziali del server che possono essere credenziali di dominio/Windows (non di dominio)/Linux (non di dominio). [Altre](add-server-credentials.md) informazioni su come fornire le credenziali e su come gestirle.

## <a name="verify-permissions"></a>Verificare le autorizzazioni

- È necessario [creare un account di sola lettura server vCenter per l'](./tutorial-discover-vmware.md#prepare-vmware) individuazione e la valutazione. Per   >  interagire con i server per eseguire l'inventario software, per l'account di sola lettura devono essere abilitati i privilegi per le **operazioni Guest** delle macchine virtuali.
- È possibile aggiungere più credenziali di dominio e non di dominio (Windows/Linux) in Gestione configurazione Appliance per l'individuazione delle applicazioni. È necessario un account utente Guest per i server Windows e un account utente normale/normale (accesso non sudo) per tutti i server Linux. [Altre](add-server-credentials.md) informazioni su come fornire le credenziali e su come gestirle.

### <a name="add-credentials-and-initiate-discovery"></a>Aggiungere le credenziali e avviare l'individuazione

1. Aprire Gestione configurazione Appliance, completare i controlli dei prerequisiti e la registrazione dell'appliance.
2. Passare al pannello **Gestisci credenziali e origini di individuazione** .
1.  In **passaggio 1: specificare** le credenziali server vCenter fare clic su **Aggiungi credenziali** per fornire le credenziali per l'account server vCenter che l'appliance userà per individuare i server in esecuzione nel server vCenter.
1. In **passaggio 2:** specificare i dettagli della server vCenter fare clic su **Aggiungi origine di individuazione** per selezionare il nome descrittivo per le credenziali dall'elenco a discesa, specificare l' **indirizzo IP/FQDN** del pannello di server vCenter istanza :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="3 in Gestione configurazione Appliance per server vCenter dettagli":::
1. In **passaggio 3: specificare le credenziali del server per eseguire l'inventario software, l'analisi delle dipendenze senza agenti e l'individuazione di istanze e database di SQL Server**, fare clic su **Aggiungi credenziali** per fornire più credenziali server per avviare l'inventario software.
1. Fare clic su **Avvia individuazione per avviare** l'individuazione server vCenter.

 Al termine dell'individuazione del server vCenter, l'appliance avvia l'individuazione delle applicazioni, dei ruoli e delle funzionalità installate (inventario software). La durata dipende dal numero di server individuati. Per i server 500, la visualizzazione dell'inventario individuato nel portale di Azure Migrate richiede circa un'ora.

## <a name="review-and-export-the-inventory"></a>Esaminare ed esportare l'inventario

Al termine dell'inventario software, è possibile esaminare ed esportare l'inventario nel portale di Azure.

1. In **Azure migrate-Servers**  >  **Azure migrate: server Assessment**, fare clic sul conteggio visualizzato per aprire la pagina **server individuati** .

    > [!NOTE]
    > In questa fase è anche possibile abilitare l'analisi delle dipendenze per i server individuati, in modo che sia possibile visualizzare le dipendenze tra i server che si desidera valutare. [Altre](concepts-dependency-visualization.md) informazioni sull'analisi delle dipendenze.

2. In **applicazioni individuate** colonna, fare clic sul numero visualizzato per esaminare le applicazioni, i ruoli e le funzionalità individuate.
4. Per esportare l'inventario, in **server individuati** fare clic su **Esporta inventario delle app**.

L'inventario delle applicazioni viene esportato e scaricato in formato Excel. Il foglio di **inventario dell'applicazione** Visualizza tutte le app individuate in tutti i server.

## <a name="discover-sql-server-instances-and-databases"></a>Individuare istanze e database di SQL Server

- L'individuazione dell'applicazione identifica anche le istanze di SQL Server in esecuzione nell'ambiente VMware.
- Se non è stata specificata l'autenticazione di Windows o SQL Server credenziali di autenticazione in Gestione configurazione Appliance, aggiungere le credenziali in modo che l'appliance possa usarle per connettersi alle rispettive istanze di SQL Server.

Una volta stabilita la connessione, appliance raccoglie i dati di configurazione e delle prestazioni delle istanze di SQL Server e dei database. I dati di configurazione SQL Server vengono aggiornati ogni 24 ore e i dati sulle prestazioni vengono acquisiti ogni 30 secondi. Di conseguenza, qualsiasi modifica apportata alle proprietà dell'istanza SQL Server e dei database, ad esempio lo stato del database, il livello di compatibilità e così via, può richiedere fino a 24 ore per l'aggiornamento nel portale.

## <a name="next-steps"></a>Passaggi successivi

- [Creare una valutazione](how-to-create-assessment.md) per i server individuati.
- [Valutare i server SQL](./tutorial-assess-sql.md) per la migrazione ad Azure SQL.
