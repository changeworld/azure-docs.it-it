---
title: Come analizzare più origini di Azure
description: Informazioni su come eseguire la scansione di un intero gruppo di risorse o di una sottoscrizione di Azure nel Catalogo dati di Azure.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 2/26/2021
ms.openlocfilehash: c57ac9ddbebcf02cb0118705b63f97fd1880b0f2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101696018"
---
# <a name="register-and-scan-azure-multiple-sources"></a>Registrare e analizzare più origini di Azure

Questo articolo illustra come registrare un'origine di Azure multipla (sottoscrizioni o gruppi di risorse di Azure) in ambito di competenza e come impostarne l'analisi.

## <a name="supported-capabilities"></a>Funzionalità supportate

Azure multiple source supporta le analisi per l'acquisizione dei metadati e dello schema nella maggior parte dei tipi di risorse di Azure supportati da questa competenza. Inoltre, i dati vengono classificati automaticamente in base alle regole di classificazione personalizzate e di sistema.

## <a name="prerequisites"></a>Prerequisiti

- Prima di registrare le origini dati, creare un account Azure. Per altre informazioni sulla creazione di un account di competenza, vedere [Guida introduttiva: creare un account Azure](create-catalog-portal.md).
- È necessario essere un amministratore dell'origine dati di competenza di Azure
- Configurazione dell'autenticazione come descritto nelle sezioni seguenti

### <a name="setting-up-authentication-for-enumerating-resources-under-a-subscription-or-resource-group"></a>Impostazione dell'autenticazione per l'enumerazione delle risorse in una sottoscrizione o in un gruppo di risorse

1. Passare alla sottoscrizione o al gruppo di risorse nel portale di Azure.  
1. Selezionare **controllo di accesso (IAM)**   dal menu di spostamento a sinistra 
1. Per aggiungere un ruolo alla sottoscrizione o al gruppo di risorse, è necessario essere un proprietario o un amministratore accesso utenti. Selezionare *+ Aggiungi* pulsante. 
1. Impostare il ruolo **lettore** e immettere il nome dell'account Azure (che rappresenta il relativo MSI) in Seleziona casella di input. Fare clic su *Salva* per completare l'assegnazione di ruolo.

### <a name="setting-up-authentication-to-scan-resources-under-a-subscription-or-resource-group"></a>Impostazione dell'autenticazione per l'analisi delle risorse in una sottoscrizione o in un gruppo di risorse

È possibile configurare l'autenticazione per più origini di Azure in due modi:

- Identità gestita
- Entità servizio

> [!NOTE]
> È necessario configurare l'autenticazione per ogni risorsa all'interno della sottoscrizione o del gruppo di risorse, che si intende registrare e analizzare. I tipi di risorse di archiviazione di Azure (archiviazione BLOB di Azure e Azure Data Lake Storage Gen2) semplificano l'aggiunta dell'identità del servizio gestito o dell'entità servizio a livello di sottoscrizione o di gruppo di risorse come lettore dati BLOB di archiviazione. Le autorizzazioni vengono quindi comunicate a ogni account di archiviazione all'interno della sottoscrizione o del gruppo di risorse. Per tutti gli altri tipi di risorse, è necessario applicare il file MSI o l'entità servizio in ogni risorsa oppure creare uno script per il dispositivo. Di seguito viene illustrato come aggiungere autorizzazioni per ogni tipo di risorsa all'interno di una sottoscrizione o di un gruppo di risorse.
    
- [Archiviazione BLOB di Azure](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Database SQL di Azure](register-scan-azure-sql-database.md)
- [Istanza gestita di database SQL di Azure](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)
 
## <a name="register-an-azure-multiple-source"></a>Registrare un'origine Azure multipla

Per registrare una nuova origine Azure multipla nel Catalogo dati, seguire questa procedura:

1. Passare all'account Purview
1. Selezionare **Origini** nel riquadro di spostamento sinistro
1. Selezionare **Registra**
1. In **registra origini** selezionare **Azure (multiplo)**
1. Selezionare **Continua**

   :::image type="content" source="media/register-scan-azure-multiple-sources/register-azure-multiple.png" alt-text="Registrare più origini di Azure":::

Nella schermata **registra origini (Azure multiple)** eseguire le operazioni seguenti:

1. Immettere un **nome** con cui l'origine dati verrà elencata nel catalogo 
1. Facoltativamente, scegliere un **gruppo di gestione** a cui filtrare
1. **Selezionare una sottoscrizione o un gruppo di risorse specifico** in una determinata sottoscrizione nell'elenco a discesa. L'ambito della registrazione verrà impostato sulla sottoscrizione selezionata o sul gruppo di risorse  
1. Selezionare una **raccolta** o crearne una nuova (facoltativo)
1. **Fine** per registrare l'origine dati

   :::image type="content" source="media/register-scan-azure-multiple-sources/azure-multiple-source-setup.png" alt-text="Configurare più origini di Azure":::

## <a name="creating-and-running-a-scan"></a>Creazione ed esecuzione di un'analisi

Per creare ed eseguire una nuova analisi, procedere come segue:

1. Passare alla sezione **origini**

1. Selezionare l'origine dati registrata

1. Fare clic su Visualizza dettagli e selezionare **+ nuova analisi** oppure usare l'icona di azione rapida di analisi nel riquadro di origine

1. Immettere il *nome* e selezionare tutti i tipi di risorsa che si vuole analizzare in questa origine

    1. È possibile lasciarla *invariata* (sono inclusi i tipi di risorse futuri che potrebbero non esistere all'interno di tale sottoscrizione o gruppo di risorse)
    1. È possibile **selezionare in modo specifico i tipi di risorse** che si desidera analizzare. Se si sceglie questa opzione, i tipi di risorse futuri che possono essere creati nella sottoscrizione o nel gruppo di risorse non verranno inclusi per le analisi, a meno che l'analisi non venga modificata in modo esplicito in futuro
    
    :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-source-scan.png" alt-text="Analisi di più origini di Azure":::

1. Selezionare le credenziali per la connessione alle risorse all'interno dell'origine dati. 
    1. È possibile selezionare una **credenziale a livello padre** come identità del servizio gestito o una specifica credenziale del tipo di entità servizio, che è possibile scegliere di usare per tutti i tipi di risorse nella sottoscrizione o nel gruppo di risorse
    1. È anche possibile selezionare in modo specifico **il tipo di risorsa e applicare credenziali diverse** per quel tipo di risorsa
    1. Ogni credenziale verrà considerata come il metodo di autenticazione per tutte le risorse in un determinato tipo
    1. È necessario impostare le credenziali scelte sulle risorse per analizzarle correttamente come descritto in questa [sezione](#Setting-up-authentication-to-scan-resources-under-a-subscription-or-resource-group) precedente
1. All'interno di ogni tipo è possibile scegliere di eseguire l'analisi di tutte le risorse o di un subset di essi in base al nome.
    1. Se si lascia l'opzione, **tutte** le future risorse di quel tipo verranno analizzate anche nelle esecuzioni di analisi future
    1. Se si selezionano account di archiviazione specifici o database SQL, le risorse future create da tale tipo nella sottoscrizione o nel gruppo di risorse non verranno incluse per le analisi, a meno che l'analisi non venga modificata in modo esplicito in futuro
 
1.  Scegliere **Continua** per procedere. Si testerà l'accesso per verificare se è stato applicato l'identità del servizio gestito come lettore nella sottoscrizione o nel gruppo di risorse. Se viene generato un messaggio di errore, seguire le istruzioni riportate [qui](#Setting-up-authentication-for-enumerating-resources-under-a-subscription-or-resource-group)

1.  Selezionare **analisi set di regole** per ogni tipo di risorsa scelto nel passaggio precedente. È anche possibile creare set di regole di analisi inline.
  :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-scan-rule-set.png" alt-text="Selezione set di regole di analisi multiple di Azure":::

1. Scegliere il trigger dell'analisi. È possibile pianificarne l'esecuzione **ogni settimana/mese** o **una volta**

1. Esaminare l'analisi e selezionare Salva per completare la configurazione   

## <a name="viewing-your-scans-and-scan-runs"></a>Visualizzazione delle analisi e delle relative esecuzioni

1. Per visualizzare i dettagli dell'origine, fare clic su **Visualizza dettagli** nel riquadro nella sezione origini. 

      :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-source-detail.png" alt-text="Dettagli sull'origine multipla di Azure"::: 

1. Visualizzare i dettagli dell'esecuzione dell'analisi passando alla pagina dei **Dettagli dell'analisi** .
    1. La *barra di stato* è un breve riepilogo sullo stato di esecuzione delle risorse figlio. Verrà visualizzato a livello di sottoscrizione o di gruppo di risorse
    1. Verde significa che ha avuto esito positivo, mentre quello rosso non è riuscito. Grigio indica che l'analisi è ancora in corso
    1. È possibile fare clic su ogni analisi per visualizzare i dettagli con granularità più fine

      :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-scan-full-details.png" alt-text="Dettagli analisi multipla di Azure":::

1. Visualizzare un riepilogo delle esecuzioni dell'analisi non riuscita recenti nella parte inferiore della pagina dei dettagli dell'origine. È anche possibile fare clic su Visualizza dettagli più granulari relativi a tali esecuzioni.

## <a name="manage-your-scans---edit-delete-or-cancel"></a>Gestire le analisi: modificare, eliminare o annullare
Per gestire o eliminare un'analisi, procedere come segue:

- Passare al centro di gestione. Selezionare le origini dati nella sezione origini e analisi e quindi selezionare l'origine dati desiderata

- Selezionare l'analisi da gestire. È possibile modificare l'analisi selezionando modifica.

- È possibile eliminare l'analisi selezionando Elimina.
- Se un'analisi è in esecuzione, è possibile annullarla

## <a name="next-steps"></a>Passaggi successivi

- [Esplorare Azure Purview Data Catalog](how-to-browse-catalog.md)
- [Eseguire ricerche in Azure Purview Data Catalog](how-to-search-catalog.md)    
