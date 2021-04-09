---
title: Usare i servizi cloud (supporto esteso) (anteprima)
description: Informazioni su come creare e distribuire un servizio cloud di Azure usando Azure Resource Manager con Visual Studio
author: ghogen
ms.service: cloud-services-extended-support
manager: jillfra
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: tutorial
ms.date: 10/5/2020
ms.author: ghogen
ms.openlocfilehash: 80aa160c53b278137467dba2afa41384c7c4f378
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101722671"
---
# <a name="create-and-deploy-a-azure-cloud-service-extended-support-using-visual-studio"></a>Creare e distribuire un servizio cloud di Azure (supporto esteso) con Visual Studio

A partire da [Visual Studio 2019 versione 16,9](https://visualstudio.microsoft.com/vs/preview/) (attualmente in anteprima), è possibile lavorare con i servizi cloud usando Azure Resource Manager (ARM), che semplifica e modernizza notevolmente la manutenzione e la gestione delle risorse di Azure. Questa funzionalità è abilitata da un nuovo servizio di Azure denominato servizi cloud (supporto esteso). È possibile pubblicare un servizio cloud esistente in servizi cloud (supporto esteso). Per informazioni su questo servizio di Azure, vedere la [documentazione relativa ai servizi cloud (supporto esteso)](overview.md).

> [!IMPORTANT]
> Servizi cloud (supporto esteso) è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-feature-for-your-subscription"></a>Registrare la funzionalità per la propria sottoscrizione
Servizi cloud (supporto esteso) è attualmente in versione di anteprima. Registrare la funzionalità per la propria sottoscrizione come indicato di seguito:

```powershell
Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute
```
Per ulteriori informazioni [, vedere Prerequisiti per la distribuzione di servizi cloud (supporto esteso)](deploy-prerequisite.md)

## <a name="create-a-project"></a>Creare un progetto

Visual Studio fornisce un modello di progetto che consente di creare un servizio cloud di Azure con supporto esteso, denominato **servizio cloud di Azure (supporto esteso)**. Un servizio cloud è un semplice servizio di Azure per utilizzo generico. Una volta creato il progetto, Visual Studio consente di configurare, eseguire il debug e distribuire il servizio cloud in Azure.

### <a name="to-create-an-azure-cloud-service-extended-support-project-in-visual-studio"></a>Per creare un progetto di servizio cloud di Azure (supporto esteso) in Visual Studio

Questa sezione illustra la creazione di un progetto di servizio cloud di Azure in Visual Studio con uno o più ruoli Web.

1. Nella finestra iniziale scegliere **Crea un nuovo progetto**.

1. Nella casella di ricerca digitare *cloud*, quindi scegliere **servizio cloud di Azure (supporto esteso)**.

   ![Nuovo servizio cloud di Azure con supporto esteso](./media/choose-project-template.png)

1. Specificare un nome per il progetto e scegliere **Crea**.

   ![Assegnare un nome al progetto](./media/configure-new-project.png)

1. Nella finestra di dialogo **Nuovo servizio cloud Microsoft Azure** scegliere i ruoli che si desidera aggiungere e fare clic sul pulsante con la freccia destra per aggiungerli alla soluzione.

    ![Selezionare nuovi ruoli del servizio cloud di Azure](./media/choose-roles.png)

1. Per rinominare un ruolo che è stato aggiunto, passare il mouse sul ruolo nella finestra di dialogo **Nuovo servizio cloud Microsoft Azure** e dal menu di scelta rapida, selezionare **Rinomina**. Una volta aggiunto, un ruolo può essere rinominato (in **Esplora soluzioni**) anche nella soluzione.

    ![Rinominare il ruolo del servizio cloud di Azure](./media/new-cloud-service-rename.png)

Il progetto Azure in Visual Studio contiene le associazioni ai progetti di ruolo nella soluzione. Il progetto include anche i *file di definizione del servizio* e i *file di configurazione del servizio*:

- **File di definizione del servizio** : definisce le impostazioni della fase di esecuzione per l'applicazione, inclusi i ruoli necessari, gli endpoint e le dimensioni della macchina virtuale.
- Il **file di configurazione del servizio** configura il numero delle istanze di un ruolo eseguite e i valori delle impostazioni definiti per un ruolo.

Per altre informazioni su questi file, vedere [configurare i ruoli per un servizio cloud di Azure con Visual Studio](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service).

## <a name="publish-a-cloud-service"></a>Pubblicare un servizio cloud

1. Creare o aprire un progetto di servizio cloud di Azure in Visual Studio.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e scegliere **Pubblica** dal menu di scelta rapida.

   ![Pagina di accesso](./media/publish-step-1.png)

1. **Account** - Selezionare un account o fare clic su **Aggiungi un account** nell'elenco a discesa degli account.

1. **Scegliere la sottoscrizione** - Scegliere la sottoscrizione da usare per la distribuzione. La sottoscrizione usata per la distribuzione di servizi cloud (supporto esteso) deve avere ruoli di proprietario o collaboratore assegnati tramite il controllo degli accessi in base al ruolo (RBAC). Se la sottoscrizione non ha uno di questi ruoli, vedere la [procedura per aggiungere un'assegnazione di ruolo](../role-based-access-control/role-assignments-steps.md) per aggiungerla prima di procedere.

1. Scegliere **Avanti** per passare alla pagina **Impostazioni** .

   ![Impostazioni comuni](./media/publish-settings.png)

1. **Servizio cloud** : usando l'elenco a discesa, selezionare un servizio cloud esistente oppure selezionare **Crea nuovo** e creare un servizio cloud. Il data center viene visualizzato tra parentesi per ogni servizio cloud. È consigliabile che la posizione del data center per il servizio cloud corrisponda a quella del data center per l'account di archiviazione.

   Se si sceglie di creare un nuovo servizio cloud, verrà visualizzata la finestra di dialogo **Crea servizio cloud (supporto esteso)** . Specificare il percorso e il gruppo di risorse che si vuole usare per il servizio cloud.

   ![Creazione di un servizio cloud con supporto esteso](./media/extended-support-dialog.png)

1. **Configurazione compilazione** - Selezionare **Debug** o **Rilascio**.

1. **Configurazione servizio** - Selezionare **Cloud** o **Locale**.

1. **Account di archiviazione** : selezionare l'account di archiviazione da usare per la distribuzione oppure **crearne uno nuovo** per creare un account di archiviazione. L'area viene visualizzata tra parentesi per ogni account di archiviazione. È consigliabile che la posizione del data center per l'account di archiviazione corrisponda a quella del data center percorso per il servizio cloud (impostazioni comuni).

   L'account di archiviazione di Azure archivia il pacchetto per la distribuzione dell'applicazione. Dopo la distribuzione dell'applicazione, il pacchetto viene rimosso dall'account di archiviazione.

1. **Key Vault** : specificare il Key Vault che contiene i segreti per il servizio cloud. Questa funzionalità è abilitata se desktop remoto è abilitato o se i certificati vengono aggiunti alla configurazione.

1. **Abilita Desktop remoto per tutti i ruoli** - Selezionare questa opzione per consentire la connessione remota al servizio. Verrà richiesto di specificare le credenziali.

   ![Impostazioni desktop remoto](./media/remote-desktop-configuration.png)

1. Scegliere **Avanti** per passare alla pagina **impostazioni di diagnostica** .

   ![Impostazioni di diagnostica](./media/diagnostics-settings.png)

   La diagnostica consente di risolvere i problemi relativi a un servizio cloud di Azure o a una macchina virtuale di Azure. Per informazioni sulla diagnostica, vedere [Configurazione della diagnostica per i servizi cloud e le macchine virtuali di Azure](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines). Per informazioni su Application Insights, vedere [Informazioni su Azure Application Insights](../azure-monitor/app/app-insights-overview.md).

1. Scegliere **Avanti** per passare alla pagina **Riepilogo** .

   ![Riepilogo](./media/publish-summary.png)

1. **Profilo di destinazione** - È possibile scegliere di creare un profilo di pubblicazione dalle impostazioni scelte. È ad esempio possibile creare un profilo per un ambiente di test e un altro per l'ambiente di produzione. Per salvare questo profilo, scegliere l'icona **Salva**. La procedura guidata crea e salva il profilo nel progetto Visual Studio. Per modificare il nome del profilo, aprire l'elenco **Profilo di destinazione**, quindi scegliere **Gestisci**.

   > [!Note]
   > Il profilo di pubblicazione viene visualizzato in Esplora soluzioni di Visual Studio e le impostazioni del profilo vengono scritte in un file con estensione azurePubxml. Le impostazioni vengono salvate come attributi dei tag XML.

1. Dopo avere configurato tutte le impostazioni per la distribuzione del progetto, selezionare **Pubblica** nella parte inferiore della finestra di dialogo. È possibile monitorare lo stato del processo nella finestra di output del **log attività di Azure** in Visual Studio.

Congratulazioni! Il progetto di servizio cloud di supporto esteso è stato pubblicato in Azure. Per eseguire di nuovo la pubblicazione con le stesse impostazioni, è possibile riutilizzare il profilo di pubblicazione oppure ripetere questi passaggi per crearne uno nuovo.

## <a name="clean-up-azure-resources"></a>Pulire le risorse di Azure

Per pulire le risorse di Azure create seguendo questa esercitazione, passare alla [portale di Azure](https://portal.azure.com), scegliere **gruppi di risorse**, trovare e aprire il gruppo di risorse usato per creare il servizio e scegliere **Elimina gruppo di risorse**.

## <a name="next-steps"></a>Passaggi successivi

Configurare l'integrazione continua (CI) usando il pulsante **Configura** della schermata di **pubblicazione** . Per ulteriori informazioni, vedere [Azure Pipelines documentazione](/azure/devops/pipelines).