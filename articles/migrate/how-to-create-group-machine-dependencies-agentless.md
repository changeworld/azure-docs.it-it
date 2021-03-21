---
title: Configurare l'analisi delle dipendenze senza agente in Azure Migrate server Assessment
description: Configurare l'analisi delle dipendenze senza agente in Azure Migrate server assessment.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 6/08/2020
ms.openlocfilehash: c3aa2aea764af8469152b007e60427724fea398a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102045854"
---
# <a name="analyze-server-dependencies-agentless"></a>Analizzare le dipendenze del server (senza agenti)

Questo articolo descrive come configurare l'analisi delle dipendenze senza agenti usando Azure Migrate: server assessment. L' [analisi delle dipendenze](concepts-dependency-visualization.md) consente di identificare e comprendere le dipendenze tra i server per la valutazione e la migrazione ad Azure.

> [!IMPORTANT]
> L'analisi delle dipendenze senza agenti è attualmente in anteprima per i server in esecuzione nell'ambiente VMware, individuati con lo strumento Azure Migrate: server assessment.
> Questa versione di anteprima è coperta dal supporto tecnico e può essere usata per i carichi di lavoro di produzione.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="current-limitations"></a>Limitazioni correnti

- Nella visualizzazione analisi delle dipendenze non è attualmente possibile aggiungere o rimuovere un server da un gruppo.
- Una mappa delle dipendenze per un gruppo di server non è attualmente disponibile.
- In un progetto Azure Migrate la raccolta dei dati sulle dipendenze può essere abilitata contemporaneamente per i server 1000. È possibile analizzare un numero maggiore di server eseguendo la sequenziazione in batch di server 1000.

## <a name="before-you-start"></a>Prima di iniziare

- Assicurarsi di aver [creato un progetto Azure migrate](./create-manage-projects.md) con lo strumento Azure migrate: server assessment.
- Esaminare i [requisiti di VMware](migrate-support-matrix-vmware.md#vmware-requirements) per eseguire l'analisi delle dipendenze.
- Esaminare i [requisiti dell'appliance](migrate-support-matrix-vmware.md#azure-migrate-appliance-requirements) prima di configurare l'appliance.
- [Esaminare i requisiti di analisi delle dipendenze](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) prima di abilitare l'analisi delle dipendenze nei server

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

- È necessario [creare un account di sola lettura server vCenter per l'](./tutorial-discover-vmware.md#prepare-vmware) individuazione e la valutazione. Per poter   >  interagire con i server per raccogliere i dati sulle dipendenze, per l'account di sola lettura devono essere abilitati i privilegi per le **operazioni Guest** delle macchine virtuali.
- È necessario un account utente in modo che server assessment possa accedere al server per raccogliere i dati delle dipendenze. [Informazioni sui requisiti](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) dell'account per i server Windows e Linux.

### <a name="add-credentials-and-initiate-discovery"></a>Aggiungere le credenziali e avviare l'individuazione

1. Aprire Gestione configurazione Appliance, completare i controlli dei prerequisiti e la registrazione dell'appliance.
2. Passare al pannello **Gestisci credenziali e origini di individuazione** .
1.  In **passaggio 1: specificare** le credenziali server vCenter fare clic su **Aggiungi credenziali** per fornire le credenziali per l'account server vCenter che l'appliance userà per individuare i server in esecuzione nel server vCenter.
1. In **passaggio 2:** specificare i dettagli della server vCenter fare clic su **Aggiungi origine di individuazione** per selezionare il nome descrittivo per le credenziali dall'elenco a discesa, specificare l' **indirizzo IP/FQDN** del pannello di server vCenter istanza :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="3 in Gestione configurazione Appliance per server vCenter dettagli":::
1. In **passaggio 3: specificare le credenziali del server per eseguire l'inventario software, l'analisi delle dipendenze senza agenti e l'individuazione di istanze e database di SQL Server**, fare clic su **Aggiungi credenziali** per fornire più credenziali server per avviare l'inventario software.
1. Fare clic su **Avvia individuazione per avviare** l'individuazione server vCenter.

 Al termine dell'individuazione del server vCenter, l'appliance avvia l'individuazione delle applicazioni, dei ruoli e delle funzionalità installate (inventario software). Durante l'inventario software, le credenziali dei server aggiunti verranno iterate sui server e convalidate per l'analisi delle dipendenze senza agenti. È possibile abilitare l'analisi delle dipendenze senza agenti per i server dal portale. È possibile selezionare solo i server in cui è stata eseguita la convalida per abilitare l'analisi delle dipendenze senza agenti.

## <a name="start-dependency-discovery"></a>Avvia individuazione dipendenza

Selezionare i server in cui si desidera abilitare l'individuazione delle dipendenze.

1. In **Azure migrate: server Assessment** fare clic su **server individuati**.
2. Scegliere il **nome del dispositivo** di cui si vuole esaminare l'individuazione.
1. È possibile visualizzare lo stato di convalida dei server sotto la colonna **dipendenze (senza agenti)** .
1. Fare clic sull'elenco a discesa **analisi delle dipendenze** .
1. Fare clic su **Aggiungi server**.
1. Nella pagina **Aggiungi server** selezionare i server in cui si desidera abilitare l'analisi delle dipendenze. È possibile abilitare il mapping delle dipendenze solo nei server in cui la convalida ha avuto esito positivo. Il ciclo di convalida successivo verrà eseguito 24 ore dopo l'ultimo timestamp di convalida.
1. Dopo aver selezionato i server, fare clic su **Aggiungi server**.

:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png" alt-text="Avviare l'analisi delle dipendenze":::

È possibile visualizzare le dipendenze circa sei ore dopo l'abilitazione dell'analisi delle dipendenze nei server. Se si vuole abilitare simultaneamente più server per l'analisi delle dipendenze, è possibile usare [PowerShell](#start-or-stop-dependency-analysis-using-powershell) .

## <a name="visualize-dependencies"></a>Visualizza dipendenze

1. In **Azure migrate: server Assessment** fare clic su **server individuati**.
1. Scegliere il **nome del dispositivo** di cui si vuole esaminare l'individuazione.
1. Cercare il server di cui si desidera esaminare le dipendenze.
1. Nella colonna **dipendenze (senza agenti)** fare clic su **Visualizza dipendenze** .
1. Modificare il periodo di tempo per cui si desidera visualizzare la mappa utilizzando l'elenco a discesa **durata tempo** .
1. Espandere il gruppo **client** per elencare i server con una dipendenza sul server selezionato.
1. Espandere il gruppo **porta** per elencare i server che hanno una dipendenza dal server selezionato.
1. Per passare alla visualizzazione mappa di uno dei server dipendenti, fare clic sul nome del server > **caricare la mappa** del server 
 :::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png" alt-text="espandere il gruppo di porte server e caricare la mappa"::: del server
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png" alt-text="Espandi gruppo di client":::

8. Espandere il server selezionato per visualizzare i dettagli a livello di processo per ogni dipendenza.
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png" alt-text="Espandere il server per visualizzare i processi":::

> [!NOTE]
> Le informazioni sul processo per una dipendenza non sono sempre disponibili. Se non è disponibile, la dipendenza viene rappresentata con il processo contrassegnato come "processo sconosciuto".

## <a name="export-dependency-data"></a>Esporta dati sulle dipendenze

1. In **Azure migrate: server Assessment** fare clic su **server individuati**.
2. Fare clic sull'elenco a discesa **analisi delle dipendenze** .
3. Fare clic su **Esporta dipendenze dell'applicazione**.
4. Nella pagina **Esporta dipendenze applicazione** scegliere il nome del dispositivo che sta scoprendo i server desiderati.
5. Selezionare l'ora di inizio e l'ora di fine. Si noti che è possibile scaricare i dati solo per gli ultimi 30 giorni.
6. Fare clic su **Esporta dipendenza**.

I dati sulle dipendenze vengono esportati e scaricati in formato CSV. Il file scaricato contiene i dati sulle dipendenze in tutti i server abilitati per l'analisi delle dipendenze. 
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/export.png" alt-text="Esporta dipendenze":::

### <a name="dependency-information"></a>Informazioni sulle dipendenze

Ogni riga nel file CSV esportato corrisponde a una dipendenza osservata nello slot di tempo specificato.

Nella tabella seguente sono riepilogati i campi del file CSV esportato. Si noti che i campi nome server, applicazione e processo vengono popolati solo per i server in cui è abilitata l'analisi delle dipendenze senza agenti.

**Nome campo** | **Dettagli**
--- | --- 
Timeslot | Timeslot durante il quale è stata osservata la dipendenza. <br/> I dati sulle dipendenze vengono acquisiti attualmente negli slot di 6 ore.
Nome del server di origine | Nome del server di origine 
Applicazione di origine | Nome dell'applicazione nel server di origine  
Processo di origine | Nome del processo nel server di origine  
Nome del server di destinazione | Nome del server di destinazione
IP di destinazione | Indirizzo IP del server di destinazione
Applicazione di destinazione | Nome dell'applicazione nel server di destinazione
Processo di destinazione | Nome del processo nel server di destinazione  
Porta di destinazione | Numero di porta nel server di destinazione

## <a name="stop-dependency-discovery"></a>Arresta individuazione dipendenza

Selezionare i server in cui si desidera arrestare l'individuazione delle dipendenze.

1. In **Azure migrate: server Assessment** fare clic su **server individuati**.
1. Scegliere il **nome del dispositivo** di cui si vuole esaminare l'individuazione.
1. Fare clic sull'elenco a discesa **analisi delle dipendenze** .
1. Fare clic su **Rimuovi server**.
1. Nella pagina **Rimuovi server** selezionare il server che si desidera arrestare per l'analisi delle dipendenze.
1. Dopo aver selezionato i server, fare clic su **Rimuovi server**.

Se si desidera arrestare la dipendenza simultaneamente su più server, è possibile utilizzare [PowerShell](#start-or-stop-dependency-analysis-using-powershell) .

## <a name="start-or-stop-dependency-analysis-using-powershell"></a>Avviare o arrestare l'analisi delle dipendenze con PowerShell

Scaricare il modulo PowerShell dal repository [Azure PowerShell Samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) su GitHub.

### <a name="log-in-to-azure"></a>Accedere ad Azure

1. Accedere alla sottoscrizione di Azure usando il cmdlet Connect-AzAccount.

    ```PowerShell
    Connect-AzAccount
    ```
    Se si usa Azure per enti pubblici, usare il comando seguente.
    ```PowerShell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
    ```

2. Selezionare la sottoscrizione in cui è stato creato il progetto di Azure Migrate 

    ```PowerShell
    select-azsubscription -subscription "Fabrikam Demo Subscription"
    ```

3. Importare il modulo di PowerShell AzMig_Dependencies scaricato

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

### <a name="enable-or-disable-dependency-data-collection"></a>Abilitare o disabilitare la raccolta dei dati sulle dipendenze

1. Ottenere l'elenco dei server individuati nel progetto Azure Migrate usando i comandi seguenti. Nell'esempio seguente il nome del progetto è FabrikamDemoProject e il gruppo di risorse a cui appartiene è FabrikamDemoRG. L'elenco dei server verrà salvato in FabrikamDemo_VMs.csv

    ```PowerShell
    Get-AzMigDiscoveredVMwareVMs -ResourceGroupName "FabrikamDemoRG" -ProjectName "FabrikamDemoProject" -OutputCsvFile "FabrikamDemo_VMs.csv"
    ```

    Nel file è possibile visualizzare il nome visualizzato del server, lo stato corrente della raccolta delle dipendenze e l'ID ARM di tutti i server individuati. 

2. Per abilitare o disabilitare le dipendenze, creare un file CSV di input. È necessario che il file includa una colonna con intestazione "ARM ID". Eventuali intestazioni aggiuntive nel file CSV verranno ignorate. È possibile creare il CSV usando il file generato nel passaggio precedente. Creare una copia del file che conserva i server per cui si desidera abilitare o disabilitare le dipendenze. 

    Nell'esempio seguente viene abilitata l'analisi delle dipendenze nell'elenco dei server nel file di input FabrikamDemo_VMs_Enable.csv.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Enable.csv -Enable
    ```

    Nell'esempio seguente, l'analisi delle dipendenze viene disabilitata nell'elenco dei server nel file di input FabrikamDemo_VMs_Disable.csv.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Disable.csv -Disable
    ```

## <a name="visualize-network-connections-in-power-bi"></a>Visualizzare le connessioni di rete in Power BI

Azure Migrate offre un modello di Power BI che è possibile usare per visualizzare le connessioni di rete di molti server contemporaneamente e filtrare in base al processo e al server. Per visualizzare, caricare il Power BI con i dati sulle dipendenze in base alle istruzioni riportate di seguito.

1. Scaricare il modulo PowerShell e il modello di Power BI dal repository [Azure PowerShell Samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) in GitHub.

2. Accedere ad Azure usando le istruzioni seguenti: 
    - Accedere alla sottoscrizione di Azure usando il cmdlet Connect-AzAccount.

        ```PowerShell
        Connect-AzAccount
        ```

    - Se si usa Azure per enti pubblici, usare il comando seguente.

        ```PowerShell
        Connect-AzAccount -EnvironmentName AzureUSGovernment
        ```

    - Selezionare la sottoscrizione in cui è stato creato il progetto di Azure Migrate

        ```PowerShell
        select-azsubscription -subscription "Fabrikam Demo Subscription"
        ```

3. Importare il modulo di PowerShell AzMig_Dependencies scaricato

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

4. Eseguire il comando seguente. Questo comando Scarica i dati sulle dipendenze in un volume CSV e li elabora per generare un elenco di dipendenze univoche che possono essere usate per la visualizzazione in Power BI. Nell'esempio seguente il nome del progetto è FabrikamDemoProject e il gruppo di risorse a cui appartiene è FabrikamDemoRG. Le dipendenze verranno scaricate per i server individuati da FabrikamAppliance. Le dipendenze univoche verranno salvate in FabrikamDemo_Dependencies.csv

    ```PowerShell
    Get-AzMigDependenciesAgentless -ResourceGroup FabrikamDemoRG -Appliance FabrikamAppliance -ProjectName FabrikamDemoProject -OutputCsvFile "FabrikamDemo_Dependencies.csv"
    ```

5. Aprire il modello di Power BI scaricato

6. Caricare i dati di dipendenza scaricati in Power BI.
    - Aprire il modello in Power BI.
    - Fare clic su **Ottieni dati** sulla barra degli strumenti. 
    - Scegliere **testo/CSV** dalle origini dati comuni.
    - Scegliere il file delle dipendenze scaricato.
    - Fare clic su **Carica**.
    - Si noterà che viene importata una tabella con il nome del file CSV. È possibile visualizzare la tabella nella barra dei campi a destra. Rinominare il AzMig_Dependencies
    - Fare clic su Aggiorna nella barra degli strumenti.

    Il grafico delle connessioni di rete e il nome del server di origine, il nome del server di destinazione, il nome del processo di origine, i sezionamenti dei nomi dei processi di destinazione devono essere attivati con i dati

7. Consente di visualizzare la mappa delle connessioni di rete che filtrano i server e i processi. Salvare il file.

## <a name="next-steps"></a>Passaggi successivi

[Server del gruppo](how-to-create-a-group.md) per la valutazione.