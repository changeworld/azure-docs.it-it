---
title: 'Esercitazione: Analizzare i dati con Azure Purview (anteprima)'
description: In questa esercitazione si esegue uno starter kit per configurare una piattaforma dati e quindi analizzare i dati delle origini dati nel catalogo di Azure Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: e373556d189e9fffc0626d19250e79c09962a004
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99062105"
---
# <a name="tutorial-scan-data-with-azure-purview-preview"></a>Esercitazione: Analizzare i dati con Azure Purview (anteprima)

> [!IMPORTANT]
> Azure Purview è attualmente in anteprima. Le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) includono termini legali aggiuntivi che si applicano a funzionalità di Azure in versione beta, in anteprima o in altro modo non ancora disponibili a livello generale.

Questa *serie di esercitazioni in cinque parti* contiene le informazioni di base per gestire la governance dell'intera piattaforma dati usando Azure Purview (anteprima). La piattaforma dati creata in questa parte dell'esercitazione verrà usate nel resto della serie.

Nella parte 1 di questa serie di esercitazioni si apprenderà come:

> [!div class="checklist"]
>
> * Creare una piattaforma dati con varie risorse dati di Azure.
> * Analizzare i dati in un catalogo.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.
* Un [account Azure Purview](create-catalog-portal.md).
* Lo [starter kit](https://github.com/Azure/Purview-Samples/blob/master/PurviewStarterKitV4.zip) che verrà distribuito nella piattaforma dati.

> [!NOTE]
> Lo starter kit è disponibile solo per Windows.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="create-a-data-estate"></a>Creare una piattaforma dati

In questa sezione si eseguono gli script dello starter kit per creare una piattaforma dati simulata. Una piattaforma dati è un portfolio composto da tutti i dati di proprietà di un'azienda. Lo script dello starter kit esegue le azioni seguenti:

* Crea un account di archiviazione BLOB di Azure e lo popola con dati.
* Crea un account di Azure Data Lake Storage Gen2.
* Cera un'istanza di Azure Data Factory e la associa ad Azure Purview.
* Configura e attiva una pipeline di attività di copia tra l'account di archiviazione BLOB di Azure e l'account di Azure Data Lake Storage Gen2.
* Esegue il push della derivazione associata da Azure Data Factory ad Azure Purview.

### <a name="prepare-to-run-the-starter-kit"></a>Preparare l'esecuzione dello starter kit

Seguire questa procedura per configurare il software client dello starter kit nel computer Windows:

1. [Scaricare lo starter kit](https://download.microsoft.com/download/9/7/9/979db3b1-0916-4997-a7fb-24e3d8f83174/PurviewStarterKitV4.zip) ed estrarne il contenuto in una posizione a scelta.


1. Nel computer immettere **PowerShell** nella casella di ricerca della barra delle applicazioni di Windows. Nell'elenco della ricerca fare clic con il pulsante destro del mouse su **Windows PowerShell** e scegliere **Esegui come amministratore**.

1. Nella finestra di PowerShell immettere il comando seguente, sostituendo `<path-to-starter-kit>` con il percorso della cartella in cui sono stati estratti i file dello starter kit.

   ```powershell
   dir -Path <path-to-starter-kit> -Recurse | Unblock-File
   ```

1. Immettere il comando seguente per installare i cmdlet di Azure.

   ```powershell
   Install-Module -Name Az -AllowClobber -Scope CurrentUser
   ```

1. Se viene visualizzato il messaggio di avviso *Per continuare è necessario il provider NuGet*, immettere **S** e quindi premere INVIO.

1. Se viene visualizzato il messaggio di avviso *Archivio non attendibile*, scegliere **Sì, tutti** e quindi premere INVIO.

L'installazione di tutti i moduli necessari con PowerShell può richiedere fino un minuto.

### <a name="collect-data-needed-to-run-the-scripts"></a>Raccogliere i dati necessari per eseguire gli script

Prima di eseguire gli script di PowerShell per i bootstrap del catalogo, ottenere i valori degli argomenti seguenti da usare:

* ID tenant
   1. Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory**.
   1. Nella sezione **Gestisci** del riquadro di spostamento selezionare **Proprietà**. Quindi selezionare l'icona Copia per **ID tenant** e salvare il valore negli Appunti. Incollare il valore in un editor di testo per usarlo in seguito.

* SubscriptionID
   1. Nel portale di Azure cercare e selezionare il nome dell'istanza di Azure Purview creata come prerequisito.
   1. Selezionare la sezione **Panoramica** e salvare il GUID per **ID sottoscrizione**.

   > [!NOTE]
   > Assicurarsi di usare la stessa sottoscrizione di quella in cui è stato creato l'account Azure Purview. Si tratta della stessa sottoscrizione inserita nell'elenco di elementi consentiti.

* CatalogName: il nome dell'account Azure Purview creato nella sezione [Creare un account Azure Purview](create-catalog-portal.md).

* CatalogResourceGroupName: il nome del gruppo di risorse in cui è stato creato l'account Azure Purview.

### <a name="verify-permissions"></a>Verificare le autorizzazioni

Seguire questa procedura per aggiungere l'utente che esegue lo script nell'account Azure Purview creato come prerequisito. Gli utenti devono avere entrambi i ruoli *Curatore dei dati di Purview* e *Amministratore delle origini dati di Purview*. 

Se si è l'utente che ha creato l'account Azure Purview, si ottiene automaticamente l'accesso ed è possibile ignorare questa sezione.

1. Passare alla pagina degli account Purview nel portale di Azure e quindi selezionare l'account da modificare.

1. Nella pagina dell'account selezionare la scheda **Controllo di accesso (IAM)** e quindi **+ Aggiungi**.

1. Selezionare **Aggiungi un'assegnazione di ruolo**.

1. Immettere **Curatore dei dati di Purview** per *Ruolo*.
 
1. Usare il valore predefinito per *Assegna accesso a*. Il valore predefinito dovrebbe essere **Utente, gruppo o entità servizio**.

1. Immettere il nome dell'utente che esegue lo script in **Seleziona**.

1. Selezionare **Salva**.

1. Ripetere i passaggi precedenti con *Ruolo* impostato su **Amministratore delle origini dati di Purview**.

Per altre informazioni, vedere [Autorizzazioni per il catalogo](catalog-permissions.md).

### <a name="run-the-client-side-setup-scripts"></a>Eseguire gli script di configurazione lato client

Una volta completata la configurazione del catalogo, eseguire gli script seguenti nella finestra di PowerShell per creare gli asset, sostituendo i segnaposto con i valori raccolti in precedenza.

1. Usare il comando seguente per passare alla directory dello starter kit. Sostituire `path-to-starter-kit` con il percorso della cartella del file estratto.

   ```powershell
   cd <path-to-starter-kit>
   ```

1. Il comando seguente imposta i criteri di esecuzione per il computer locale. Quando viene chiesto di specificare se cambiare i criteri di esecuzione, scegliere **Sì, tutti**.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted
   ```

1. Connettersi ad Azure con il comando seguente. Sostituire i segnaposto `TenantID` e `SubscriptionID` .

   ```powershell
   .\RunStarterKit.ps1 -ConnectToAzure -TenantId <TenantID> `
   -SubscriptionId <SubscriptionID>
   ```

   Quando si esegue il comando, è possibile che venga visualizzata un finestra popup che chiede di accedere con le credenziali di Azure Active Directory.


1. Usare il comando seguente per eseguire lo starter kit. Sostituire i segnaposto `CatalogName`, `TenantID`, `SubscriptionID`, `NewResourceGroupName` e `CatalogResourceGroupName`. Per `NewResourceGroupName`, usare un nome univoco, composto esclusivamente da caratteri alfanumerici minuscoli, per il gruppo di risorse che conterrà la piattaforma dati.

   > [!IMPORTANT]
   > Per **newresourcegroupname** è possibile usare un massimo di 17 caratteri composti solo da numeri e lettere in minuscolo. **Non è consentito usare lettere in maiuscolo e caratteri speciali.** Questo vincolo deriva dalle regole di denominazione degli account di archiviazione.

   ```powershell
   .\RunStarterKit.ps1 -CatalogName <CatalogName> -TenantId <TenantID>`
   -ResourceGroup <newresourcegroupname> `
   -SubscriptionId <SubscriptionID> `
   -CatalogResourceGroup <CatalogResourceGroupName>
   ```

La configurazione dell'ambiente può richiedere fino a 10 minuti. Durante questo periodo potrebbero essere visualizzate diverse finestre popup, che è possibile ignorare. Non chiudere la finestra **BlobDataCreator.exe**, perché si chiuderà automaticamente al termine.

Quando viene visualizzato il messaggio `Executing Copy pipeline xxxxxxxxxx-487e-4fc4-9628-92dd8c2c732b`, attendere che venga avviata e completata l'esecuzione di un'altra istanza **BlobDataCreator.exe**.

> [!IMPORTANT]
> Se il numero di attività attive smette di diminuire, è possibile uscire dalla finestra di creazione di BLOB e premere INVIO nella finestra di PowerShell

Al termine del processo, viene creato un gruppo di risorse con il nome specificato. L'account di Azure Data Factory, l'account di archiviazione BLOB di Azure e l'account di Azure Data Lake Storage Gen2 sono tutti contenuti in questo gruppo di risorse. Il gruppo di risorse è contenuto nella sottoscrizione specificata.

## <a name="scan-data-into-the-catalog"></a>Analizzare i dati in un catalogo

L'analisi è un processo per cui il catalogo si connette direttamente a un'origine dati in base a una pianificazione specificata dall'utente. Il catalogo riflette la piattaforma dati di un'azienda tramite analisi, derivazione, portale e API. Gli obiettivi includono l'esame del contenuto, l'estrazione di schemi e il tentativo di comprendere la semantica. In questa sezione si configura un'analisi delle origini dati generate con lo starter kit.

Lo starter kit eseguito ha creato due origini dati, archiviazione BLOB di Azure e Azure Data Lake Storage Gen2. È possibile analizzare queste origini dati nel catalogo una alla volta.

### <a name="authenticate-to-your-storage-with-managed-identity"></a>Eseguire l'autenticazione nell'archiviazione con un'identità gestita

Quando si crea l'account Azure Purview, viene automaticamente creata un'identità gestita con lo stesso nome. Prima di analizzare i dati, è necessario assegnare al ruolo di Azure Purview le autorizzazioni per gli account di archiviazione.

1. Passare al gruppo di risorse creato con lo starter kit e selezionare l'account di archiviazione BLOB.

1. Selezionare **Controllo di accesso (IAM)** nel menu di spostamento sinistro. Quindi selezionare **+ Aggiungi**.

1. Impostare Ruolo su **Lettore dei dati del BLOB di archiviazione** e immettere il nome dell'account Azure Purview per **Seleziona**. Quindi selezionare **Salva** per fornire questa assegnazione di ruolo all'account Purview.

   :::image type="content" source="media/tutorial-scan-data/add-role-assignment.png" alt-text="Aggiungere un'assegnazione di ruolo":::

1. Ripetere i passaggi precedenti per Azure Data Lake Storage Gen2.

### <a name="scan-your-data-sources"></a>Analizzare le origini dati

1. Passare alla risorsa di Azure per l'uso nel [portale di Azure](https://portal.azure.com) e selezionare *Open di competenza Studio*. Si viene automaticamente reindirizzati nella home page di Purview Studio.

1. Selezionare **Origini** nella pagina Web del catalogo, quindi selezionare **Registra**. Selezionare **Archiviazione BLOB di Azure** e quindi **Continua**.

   :::image type="content" source="media/tutorial-scan-data/add-blob-storage.png" alt-text="Registrare la risorsa archiviazione BLOB":::

1. Nella pagina **Register sources** (Registra origini) immettere un **Nome**. Scegliere il **nome dell'account di archiviazione** BLOB creato in precedenza con lo starter kit. L'account è denominato `<YourResourceGroupName>adcblob`. Selezionare **Fine**.

   :::image type="content" source="./media/tutorial-scan-data/register-azure-blob-storage.png" alt-text="Screenshot che mostra le impostazioni per registrare un'origine dati archiviazione BLOB di Azure." border="true":::

1. Nella visualizzazione mappa **Origini dati** selezionare **Nuova analisi** nel riquadro Archiviazione BLOB di Azure.

   :::image type="content" source="./media/tutorial-scan-data/select-setup-scan.png" alt-text="Screenshot che mostra come selezionare la configurazione di un'analisi da un'origine dati." border="true":::

1. Nella pagina **Analisi** immettere un nome per l'analisi, selezionare l'identità gestita nell'elenco a discesa **Credenziali**, quindi selezionare **Continua**.

   :::image type="content" source="media/tutorial-scan-data/scan-blob.png" alt-text="Analizzare archiviazione BLOB in Azure Purview":::

1. È possibile impostare l'ambito dell'analisi su singoli BLOB. In questa esercitazione mantenere tutti gli asset selezionati per analizzarli tutti, quindi selezionare **Continua**.

   :::image type="content" source="media/tutorial-scan-data/scope-your-scan.png" alt-text="Impostare l'ambito dell'analisi dell'archiviazione":::

1. Selezionare il set di regole di analisi predefinito, quindi selezionare **Continua**.

1. È possibile configurare un trigger per le analisi ricorrenti. Per questa esercitazione selezionare **Una volta** e quindi **Continua**.

1. Selezionare **Salva ed esegui** per completare l'analisi.

1. Ripetere i passaggi precedenti per l'account di Azure Data Lake Storage Gen2.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:
> [!div class="checklist"]
>
> * Eseguire lo script dello starter kit per configurare un ambiente di piattaforma dati.
> * Analizzare i dati in un catalogo di Azure Purview.

Passare all'esercitazione successiva per informazioni su come esplorare la home page e cercare un asset.

> [!div class="nextstepaction"]
> [Esplorare la home page e cercare un asset](tutorial-asset-search.md)
